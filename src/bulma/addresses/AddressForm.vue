<template>
    <modal show
        v-on="$listeners"
        portal="address-form">
        <enso-form class="box has-background-light"
            v-bind="$attrs"
            v-on="$listeners"
            @loaded="ready = true; $emit('loaded')"
            ref="form">
            <template v-for="customField in customFields"
                v-slot[customField.name]="{ errors }">
                <slot :name="customField.name"
                    :field="customField"
                    :errors="errors"/>
            </template>
        </enso-form>
    </modal>
</template>

<script>

import { library } from '@fortawesome/fontawesome-svg-core';
import { faLocationArrow } from '@fortawesome/free-solid-svg-icons';
import { Modal } from '@enso-ui/modal/bulma';
import { EnsoForm } from '@enso-ui/forms/bulma';

library.add(faLocationArrow);

export default {
    name: 'AddressForm',

    components: { Modal, EnsoForm },

    data: () => ({
        ready: false,
    }),

    computed: {
        customFields() {
            return this.ready
                ? this.$refs.form.customFields
                : [];
        },
    },

    methods: {
        field(field) {
            return this.ready
                ? this.$refs.form.field(field)
                : null;
        },
        param(param) {
            return this.ready
                ? this.$refs.form.param(param)
                : null;
        },
    },
};

</script>

<style lang="scss">

    .address-form {
        .modal-content {
            width: 70%;
        }
    }

</style>
