<template>
    <tabs>
        <template #label="{ tab }">
            <span class="icon is-small"
                v-if="icons">
                <fa :icon="tab"/>
            </span>
            <span v-else>
                {{ i18n(tab) }}
            </span>
            <span class="tag is-dark counter"
                v-if="has(tab) && count[tab] > 0">
                {{ count[tab] }}
            </span>
        </template>
        <slot :count="count"/>
    </tabs>
</template>

<script>
import { FontAwesomeIcon as Fa } from '@fortawesome/vue-fontawesome';
import { EnsoTabs as Tabs } from '@enso-ui/tabs/bulma';

export default {
    name: 'Accessories',

    components: { Fa, Tabs },

    inject: ['i18n'],

    props: {
        icons: {
            type: Boolean,
            default: false,
        },
    },

    data: () => ({
        count: {},
    }),

    methods: {
        has(tab) {
            return this.count[tab] !== undefined;
        },
    },
};
</script>

<style lang="scss">
    .tag.counter {
        margin-left: .25em;
        padding-left: .5em;
        padding-right: .5em;
        height: 1.8em;
    }
</style>
