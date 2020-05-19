<template>
    <div class="box has-background-light raises-on-hover address has-padding-large"
        @mouseover="controls = true"
        @mouseleave="controls = !confirmation ? false : controls">
        <div class="media">
            <div class="media-content">
                <span class="icon is-pulled-right has-text-success"
                    v-tooltip="i18n('default')"
                    v-if="address.isDefault">
                    <fa icon="anchor"/>
                </span>
                <p>
                    {{ address.street }}
                </p>
                <p v-if="address.additional">
                    {{ address.additional }}
                </p>
                <p>
                    <span v-if="address.locality">
                        {{ address.locality }}
                    </span>
                    <span v-if="address.city">
                        {{ address.city }}
                    </span>
                    <span v-if="address.region">
                        {{ address.region }}
                    </span>
                </p>
                <p v-if="address.postalArea">
                    {{ i18n('Postal Area') }}  {{ address.postalArea }}
                </p>
                <p>
                    <span class="icon is-small">
                        <fa icon="globe"
                            size="xs"/>
                    </span>
                    <span>
                        {{ address.country }}
                    </span>
                    <span class="is-pulled-right is-flex"
                        v-if="controls">
                        <a class="button is-naked is-small"
                            @click="$emit('edit')">
                            <span class="icon">
                                <fa icon="pencil-alt"/>
                            </span>
                        </a>
                        <a class="button is-naked is-small"
                            @click="$emit('make-default')">
                            <span class="icon">
                                <fa icon="anchor"/>
                            </span>
                        </a>
                        <confirmation placement="top"
                            @show="confirmation = true"
                            @hide="confirmation = controls = false"
                            @confirm="$emit('delete')">
                            <a class="button is-naked is-small">
                                <span class="icon">
                                    <fa icon="trash-alt"/>
                                </span>
                            </a>
                        </confirmation>
                    </span>
                </p>
                <p v-if="address.obs">
                    <span class="icon is-small">
                        <fa icon="sticky-note"
                            size="xs"/>
                    </span>
                    {{ address.obs }}
                </p>
            </div>
        </div>
    </div>
</template>

<script>
import { VTooltip } from 'v-tooltip';
import { library } from '@fortawesome/fontawesome-svg-core';
import {
    faPencilAlt, faAnchor, faGlobe, faStickyNote, faTrashAlt,
} from '@fortawesome/free-solid-svg-icons';
import Confirmation from '@enso-ui/confirmation/bulma';

library.add(faPencilAlt, faAnchor, faGlobe, faStickyNote, faTrashAlt);

export default {
    name: 'AddressCard',

    directives: { tooltip: VTooltip },

    components: { Confirmation },

    inject: ['i18n'],

    props: {
        address: {
            type: Object,
            required: true,
        },
    },

    data: () => ({
        controls: false,
        confirmation: false,
    }),
};
</script>

<style lang="scss">
    .address {
        .media .media-content {
            min-height: 144px;
        }
    }
</style>
