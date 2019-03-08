<template>
    <card :collapsed="collapsed">
        <card-header class="has-background-light">
            <template v-slot:title>
                <span class="icon is-small has-margin-right-small">
                    <fa :icon="icon"/>
                </span>
                {{ displayTitle }}
            </template>
            <template v-slot:controls>
                <card-refresh @refresh="fetch"/>
                <card-badge :label="count"/>
                <card-collapse/>
            </template>
        </card-header>
        <card-content class="is-paddingless">
            <documents :id="id"
                ref="documents"
                :type="type"
                :query="query"
                @update="count = $refs.documents.count; $refs.card.resize()"/>
        </card-content>
    </card>
</template>

<script>
import { mapState } from 'vuex';
import { library } from '@fortawesome/fontawesome-svg-core';
import { faCopy, faPlusSquare } from '@fortawesome/free-solid-svg-icons';
import {
    Card, CardHeader, CardRefresh, CardCollapse, CardBadge, CardContent,
} from '@enso-ui/card/bulma';
import Documents from './Documents.vue';

library.add(faCopy, faPlusSquare);

export default {
    name: 'DocumentsCard',

    inject: ['i18n'],

    components: {
        Card, CardHeader, CardRefresh, CardCollapse, CardBadge, CardContent, Documents,
    },

    props: {
        icon: {
            type: [String, Array, Object],
            default: () => faCopy,
        },
        collapsed: {
            type: Boolean,
            default: false,
        },
        id: {
            type: Number,
            required: true,
        },
        type: {
            type: String,
            required: true,
        },
        title: {
            type: String,
            default: '',
        },
    },

    data: () => ({
        query: '',
        count: 0,
    }),

    computed: {
        ...mapState('layout', ['isMobile']),
        isEmpty() {
            return this.count === 0;
        },
        uploadLink() {
            return route('core.documents.store');
        },
        displayTitle() {
            return !this.isMobile
                ? this.title || this.i18n('Documents')
                : null;
        },
    },

    watch: {
        count() {
            this.$refs.card.resize();
        },
    },
};
</script>

<style scoped>
    .wrapper {
        max-height: 500px;
        overflow-y: auto;
    }
</style>
