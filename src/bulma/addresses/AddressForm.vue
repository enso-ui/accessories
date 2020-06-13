<template>
    <modal show
        portal="address-form"
        v-on="$listeners">
        <enso-form class="box has-background-light"
            :params="params"
            :key="key"
            v-bind="$attrs"
            v-on="$listeners"
            @ready="setFields"
            disable-state
            ref="form">
            <template v-slot:actions-left
                v-if="canAccess('core.addresses.localize')">
                <a class="button is-warning"
                   :class="{'loading': loading}"
                    @click="localize">
                    <span class="is-hidden-mobile">
                        {{ i18n('Localize') }}
                    </span>
                    <span class="icon">
                        <fa icon="map-pin"/>
                    </span>
                    <span class="is-hidden-mobile"/>
                </a>
            </template>
            <template v-slot:country_id="{ field }">
                <form-field :field="field"
                    @input="rerender"/>
            </template>
            <template v-slot:region_id="{ field, errors }">
                <form-field :field="field"
                    @input="
                        localityParams.region_id = $event;
                        errors.clear(field.name);
                    "/>
            </template>
            <template v-slot:locality_id="{ field, errors }">
                <form-field :field="field"
                    :params="localityParams"
                    @input="
                        errors.clear(field.name)
                    "/>
            </template>
        </enso-form>
    </modal>
</template>

<script>
import { library } from '@fortawesome/fontawesome-svg-core';
import { faLocationArrow, faMapPin } from '@fortawesome/free-solid-svg-icons';
import { Modal } from '@enso-ui/modal/bulma';
import { EnsoForm } from '@enso-ui/forms/bulma';
import { FormField } from '@enso-ui/forms/bulma';

library.add(faLocationArrow, faMapPin);

export default {
    name: 'AddressForm',

    components: { Modal, EnsoForm, FormField },

    props: {
        id: {
            type: [String, Number],
            required: true,
        },
        type: {
            type: String,
            default: null,
        },
    },

    inject: ['canAccess', 'errorHandler', 'i18n', 'route'],

    data: () => ({
        key: 1,
        form: null,
        loading: false,
        params: {
            countryId: null,
        },
        localityParams: {
            region_id: null,
        },
    }),

    methods: {
        localize() {
            this.loading = true;
            const address = this.form.routeParams('address');

            axios.get(this.route('core.addresses.localize', address))
                .then(({ data }) => {
                    const { lat, lng } = data;

                    if (!lat && !lng) {
                        this.$toastr.warning(this.i18n("Unable to determine the address' position"));
                    } else {
                        this.$refs.form.field('lat').value = lat;
                        this.$refs.form.field('long').value = lng;
                    }
                    
                    this.loading = false;
                }).catch(error => {
                    this.loading = false;
                    this.errorHandler(error);
                });
        },
        rerender(countryId) {
            this.params.countryId = countryId;
            this.key++;
        },
        setFields({ form }) {
            this.form = form;
            this.form.field('addressable_id').value = this.id;
            this.form.field('addressable_type').value = this.type;
            this.localityParams.region_id = this.form.field('region_id').value;
            this.$emit('form-loaded');
        },
    },
};
</script>
