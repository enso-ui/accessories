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
import { faLocationArrow } from '@fortawesome/free-solid-svg-icons';
import { Modal } from '@enso-ui/modal/bulma';
import { EnsoForm } from '@enso-ui/forms/bulma';
import { FormField } from '@enso-ui/forms/bulma';

library.add(faLocationArrow);

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

    data: () => ({
        key: 1,
        form: null,
        params: {
            countryId: null,
        },
        localityParams: {
            region_id: null,
        },
    }),

    methods: {
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
