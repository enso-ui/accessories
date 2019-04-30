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
                :key="index">
                <address-card :address="address"
                    @set-default="setDefault(address)"
                    @edit="edit(address)"
                    @delete="destroy(address, index)">
                    <template v-slot:address
                        :address="address">
                        <slot name="address"
                            :address="address"/>
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
            <template v-for="field in customFields"
                v-slot:[field.name]="props">
                <slot :name="field.name"
                    v-bind="props"/>
            </template>
        </address-form>
    </div>
</template>

<script>
import { mapState } from 'vuex';
import { faPlus, faSync, faSearch } from '@fortawesome/free-solid-svg-icons';
import { library } from '@fortawesome/fontawesome-svg-core';
import AddressCard from './AddressCard.vue';
import AddressForm from './AddressForm.vue';

library.add(faPlus, faSync, faSearch);

export default {
    name: 'Addresses',

    components: { AddressCard, AddressForm },

    inject: ['errorHandler', 'i18n'],

    props: {
        id: {
            type: [String, Number],
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
        customFields: [],
    }),

    computed: {
        ...mapState('layout', ['isMobile']),
        filteredAddresses() {
            const query = this.internalQuery.toLowerCase();

            return query
                ? this.addresses.filter(
                    ({ city, street }) => city.toLowerCase().indexOf(query) > -1
                        || street.toLowerCase().indexOf(query) > -1,
                )
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
                    this.$emit('update');
                    this.loading = false;
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
                    this.$emit('update');
                    this.loading = false;
                }).catch(this.errorHandler);
        },
        setFields() {
            this.$refs.form.field('addressable_type').value = this.type;
            this.$refs.form.field('addressable_id').value = this.id;
            this.customFields = this.$refs.form.customFields;
            this.$emit('form-loaded');
        },
        reset() {
            this.path = null;
            this.customFields = [];
        },
    },
};
</script>

<style lang="scss">
    .addresses-wrapper .controls {
        display: flex;
        justify-content: center;
    }
</style>
