<template>
    <div class="box has-background-light raises-on-hover address">
        <div class="media">
            <div class="media-content">
                <span class="icon is-pulled-right has-text-success"
                    v-tooltip="i18n('default')"
                    v-if="address.isDefault">
                    <fa icon="anchor"/>
                </span>
                <slot name="address" :address="address">
                    <strong v-if="address.number">
                        {{ address.number }}
                    </strong>
                    <strong>
                        {{ address.street }}
                    </strong>
                    <strong v-if="address.streetType">
                        {{ i18n(address.streetType) }},
                    </strong>
                    <br>
                    <strong v-if="address.building">
                        <span class="has-text-grey">
                            {{ i18n(address.buildingType) }}
                        </span>
                        {{ address.building }},
                    </strong>
                    <strong v-if="address.entry">
                        <span class="has-text-grey">
                            {{ i18n('Entry') }}
                        </span>
                        {{ address.entry }},
                    </strong>
                    <strong v-if="address.floor">
                        <span class="has-text-grey">
                            {{ i18n('Floor') }}
                        </span>
                        {{ address.floor }},
                    </strong>
                    <strong v-if="address.apartment">
                        <span class="has-text-grey">
                            {{ i18n('Apartment') }}
                        </span>
                        {{ address.apartment }},
                    </strong>
                    <br>
                    <strong v-if="address.subAdministrativeArea">
                        {{ address.subAdministrativeArea }},
                    </strong>
                    <strong v-if="address.city">
                        {{ address.city }},
                    </strong>
                    <br>
                    <strong v-if="address.postalArea">
                        {{ address.postalArea }},
                    </strong>
                    <strong v-if="address.administrativeArea">
                        {{ address.administrativeArea }},
                    </strong>
                    <br>
                    <span class="icon">
                        <fa icon="globe"/>
                    </span>
                    <strong>
                        {{ address.country }}
                    </strong>
                    <br>
                    <span class="icon"
                        v-if="address.obs">
                        <fa icon="sticky-note"/>
                    </span>
                     {{ address.obs }}
                </slot>
            </div>
        </div>
        <div class="details has-text-centered has-margin-top-medium">
            <a class="button is-naked"
                @click="$emit('edit')">
                <span class="icon">
                    <fa icon="pencil-alt"/>
                </span>
            </a>
            <a class="button is-naked"
                @click="$emit('set-default')">
                <span class="icon">
                    <fa icon="anchor"/>
                </span>
            </a>
            <confirmation placement="top"
                @confirm="$emit('delete')">
                <a class="button is-naked">
                    <span class="icon">
                        <fa icon="trash-alt"/>
                    </span>
                </a>
            </confirmation>
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
};
</script>

<style lang="scss">
    .address {
        .media .media-content {
            min-height: 148px;
        }

        .details {
            display: flex;
            justify-content: center;
        }
    }
</style>
