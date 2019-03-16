<template>
    <tabs v-on="$listeners">
        <template v-slot:label="{ tab }">
            <span>
                {{ i18n(tab) }}
            </span>
            <span class="tag is-dark counter">
                {{ count[tab] }}
            </span>
        </template>
        <slot :count="count"/>
    </tabs>
</template>

<script>
import { EnsoTabs as Tabs } from '@enso-ui/tabs/bulma';

export default {
    name: 'Accessories',

    components: { Tabs },

    inject: ['i18n'],

    data: () => ({
        count: {},
    }),
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
