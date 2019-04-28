<template>
    <div class="addresses-wrapper">
        <div class="controls">
            <button class="button"
                    @click="create()">
                <span v-if="!isMobile">
                    {{ i18n('New Address') }}
                </span>
                <span class="icon">
                    <fa icon="plus"/>
                </span>
            </button>
            <button class="button has-margin-left-small"
                @click="fetch()">
                <span v-if="!isMobile">
                    {{ i18n('Reload') }}
                </span>
                <span class="icon">
                    <fa icon="sync"/>
                </span>
            </button>
            <p class="control has-icons-left has-icons-right has-margin-left-large">
                <input class="input is-rounded"
                    type="text"
                    v-model="internalQuery"
                    :placeholder="i18n('Filter')">
                <span class="icon is-small is-left">
                    <fa icon="search"/>
                </span>
                <span class="icon is-small is-right clear-button"
                    v-if="internalQuery"
                    @click="internalQuery = ''">
                    <a class="delete is-small"/>
                </span>
            </p>
        </div>
        <div class="columns is-multiline has-margin-top-large">
            <div class="column is-half-tablet is-one-third-widescreen"
                v-for="(address, index) in filteredAddresses"
                :key="address.id">
                <address-card :address="address"
                    @set-default="setDefault(address)"
                    @edit="edit(address)"
                    @delete="destroy(address, index)">
                    <template v-slot:address="{ address }">
                        <p>
                            <span v-if="address.streetType">
                                {{ i18n(address.streetType) }}
                            </span>
                            <span v-if="address.street">
                                {{ address.street }}
                            </span>
                            <span v-if="address.number">
                                <span>
                                    {{ i18n('Number') }}
                                </span>
                                {{ address.number }}
                            </span>
                        </p>
                        <p>
                            <span v-if="address.building">
                                {{ i18n(address.buildingType) }}: {{ address.building }},
                            </span>
                            <span v-if="address.entry">
                                {{ i18n('Entry') }}: {{ address.entry }},
                            </span>
                            <span v-if="address.floor">
                                {{ i18n('Floor') }}: {{ address.floor }},
                            </span>
                            <span v-if="address.apartment">
                                {{ i18n('Apt') }}: {{ address.apartment }},
                            </span>
                        </p>
                        <p>
                            <span v-if="address.localityName">
                                {{ address.localityName }},
                            </span>
                            <span v-if="address.neighbourhood">
                                {{ address.neighbourhood }}
                            </span>
                            <span v-if="address.sector">
                                {{ i18n('Sector') }}: {{ address.sector }}
                            </span>
                            <br>
                            <span v-if="address.postalArea">
                                {{ address.postalArea }}
                            </span>
                        </p>
                        <p>
                            <span class="icon">
                                <fa icon="globe"/>
                            </span>
                            {{ address.country }} <br>
                            <span class="icon" v-if="address.obs">
                                <fa icon="sticky-note"/>
                            </span>
                            {{ address.obs }}
                        </p>
                    </template>
                </address-card>
            </div>
        </div>
        <address-form :path="path"
            @loaded="setFields()"
            @close="reset();"
            @submit="fetch(); reset();"
            ref="form"
            v-if="path">
            <template v-slot:county_id="props">
                <form-field v-bind="props"
                    @input="
                        localityParams.county_id = $event;
                        props.errors.clear(props.field.name);
                    "/>
            </template>
            <template v-slot:locality_id="props">
                <form-field v-bind="props"
                    :params="localityParams"
                    @input="
                        props.errors.clear(props.field.name);
                        sectors = props.field.value === bucharestId
                    "/>
            </template>
            <template v-slot:sector="props">
                <form-field v-bind="props"
                    @input="props.errors.clear(props.field.name)"/>
            </template>
        </address-form>
    </div>
</template>

<script>

import { mapState } from 'vuex';
import { library } from '@fortawesome/fontawesome-svg-core';
import { faPlus, faSync, faSearch } from '@fortawesome/free-solid-svg-icons';
import { FormField } from '@enso-ui/forms/bulma';
import AddressCard from './AddressCard.vue';
import AddressForm from './AddressForm.vue';

library.add(faPlus, faSync, faSearch);

export default {
    name: 'RoAddresses',

    components: {
        AddressCard, AddressForm, FormField,
    },

    inject: ['errorHandler', 'i18n'],

    props: {
        id: {
            type: Number,
            required: true,
        },
        type: {
            type: String,
            default: null,
        },
        query: {
            type: String,
            default: '',
        },
    },

    data: () => ({
        loading: false,
        addresses: [],
        path: null,
        internalQuery: '',
        localityParams: {
            county_id: null,
        },
        sectors: false,
        bucharestId: null,
    }),

    computed: {
        ...mapState('layout', ['isMobile']),
        filteredAddresses() {
            const query = this.internalQuery.toLowerCase();

            return query
                ? this.addresses.filter(({ city, street }) => city.toLowerCase().indexOf(query) > -1
                    || street.toLowerCase().indexOf(query) > -1)
                : this.addresses;
        },
        count() {
            return this.filteredAddresses.length;
        },
        params() {
            return {
                addressable_id: this.id,
                addressable_type: this.type,
            };
        },
    },

    watch: {
        count() {
            this.$emit('update');
        },
        sectors() {
            if (!this.sectors) {
                this.$refs.form.field('sector').value = null;
                this.$refs.form.field('sector').meta.readonly = true;
            } else {
                this.$refs.form.field('sector').meta.readonly = false;
            }
        },
        query() {
            this.internalQuery = this.query;
        },
    },

    created() {
        this.fetch();
    },

    methods: {
        fetch() {
            this.loading = true;

            axios.get(route('core.addresses.index'), { params: this.params })
                .then(({ data }) => {
                    this.addresses = data;
                    this.loading = false;
                    this.$emit('update');
                }).catch(this.errorHandler);
        },
        edit(address) {
            this.path = route('core.addresses.edit', address.id);
        },
        create() {
            this.path = route('core.addresses.create', this.params);
        },
        setDefault(address) {
            this.loading = true;

            axios.patch(route('core.addresses.setDefault', address.id))
                .then(() => this.fetch())
                .catch(this.errorHandler);
        },
        destroy(address, index) {
            this.loading = true;

            axios.delete(route('core.addresses.destroy', address.id))
                .then(() => {
                    this.addresses.splice(index, 1);
                    this.loading = false;
                }).catch(this.errorHandler);
        },
        setFields() {
            this.$refs.form.field('addressable_type').value = this.type;
            this.$refs.form.field('addressable_id').value = this.id;
            this.localityParams.county_id = this.$refs.form.field('county_id').value;
            this.bucharestId = this.$refs.form.param('bucharestId');
            this.$emit('form-loaded');
        },
        reset() {
            this.path = null;
        },
    },
};

</script>

<style lang="scss" scoped>

    .controls {
        display: flex;
        justify-content: center;
    }

</style>
