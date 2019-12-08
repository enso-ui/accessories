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
                <slot name="address"
                    :address="address">
                    <p>
                        <strong v-if="address.number">
                            {{ address.number }}
                        </strong>
                        <strong>
                            {{ address.street }}
                        </strong>
                        <strong v-if="address.streetType">
                            {{ i18n(address.streetType) }}
                        </strong>
                    </p>
                    <p>
                        <strong v-if="address.building">
                            <span class="has-text-grey">
                                {{ i18n(address.buildingType) }}
                            </span>
                            {{ address.building }}
                        </strong>
                    </p>
                    <p>
                        <strong v-if="address.entry">
                            <span class="has-text-grey">
                                {{ i18n('Entry') }}
                            </span>
                            {{ address.entry }}
                        </strong>
                        <strong v-if="address.floor">
                            <span class="has-text-grey">
                                {{ i18n('Floor') }}
                            </span>
                            {{ address.floor }}
                        </strong>
                        <strong v-if="address.apartment">
                            <span class="has-text-grey">
                                {{ i18n('Apt') }}
                            </span>
                            {{ address.apartment }}
                        </strong>
                    </p>
                    <p>
                        <strong v-if="address.subAdministrativeArea">
                            {{ address.subAdministrativeArea }}
                        </strong>
                        <strong v-if="address.city">
                            {{ address.city }}
                        </strong>
                        <br>
                        <strong v-if="address.postalArea">
                            {{ address.postalArea }}
                        </strong>
                        <strong v-if="address.administrativeArea">
                            {{ address.administrativeArea }}
                        </strong>
                    </p>
                </slot>
                <p>
                    <span class="icon is-small">
                        <fa icon="globe"
                            size="xs"/>
                    </span>
                    <strong>
                        {{ address.country }}
                    </strong>
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
                <p>
                    <span class="icon is-small"
                        v-if="address.obs">
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
            min-height: 148px;
        }
    }
</style>
