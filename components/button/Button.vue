<template>
    <a v-if="href" :class="classes" :href="href" v-bind="$attrs" role="button" class="mdc-button">
        <slot name="icon" />
        <span class="mdc-button__label"><slot /></span>
        <slot name="trailingIcon" />
        <div v-if="ripple" class="mdc-button__ripple"></div>
    </a>
    <button v-else :class="classes" v-bind="$attrs" class="mdc-button">
        <slot name="icon" />
        <span class="mdc-button__label"><slot /></span>
        <slot name="trailingIcon" />
        <div v-if="ripple" class="mdc-button__ripple"></div>
    </button>
</template>

<script>
import { MDCRipple } from '@material/ripple'
import { baseComponentMixin, themeClassMixin } from '../base'

export default {
    mixins: [baseComponentMixin, themeClassMixin],
    props: {
        raised: {
            type: Boolean,
            default: false
        },
        unelevated: {
            type: Boolean,
            default: false
        },
        outlined: {
            type: Boolean,
            default: false
        },
        dense: {
            type: Boolean,
            default: false
        },
        href: {
            type: String,
            default: ""
        },
        ripple: {
            type: Boolean,
            default: true
        }
    },
    data() {
        return {
            mdcRipple: undefined,
            slotObserver: undefined
        };
    },
    computed: {
        classes() {
            return {
                "mdc-button--raised": this.raised,
                "mdc-button--unelevated": this.unelevated,
                "mdc-button--outlined": this.outlined,
                "mdc-button--dense": this.dense
            };
        }
    },
    watch: {
        classes() {
            this.reInstantiateRipple();
        },
        ripple() {
            this.reInstantiateRipple();
        }
    },
    mounted() {
        this.updateSlot();
        this.slotObserver = new MutationObserver(() => this.updateSlot());
        this.slotObserver.observe(this.$el, {
            childList: true,
            subtree: true
        });
        if (this.ripple) this.mdcRipple = MDCRipple.attachTo(this.$el);
    },
    beforeUnmount() {
        this.slotObserver.disconnect();
        if (this.mdcRipple) {
            this.mdcRipple.destroy();
        }
    },
    methods: {
        updateSlot() {
            if (this.$slots.icon || this.$slots.trailingIcon) {
                Array.from(this.$el.querySelectorAll(".material-icons"), n => {
                    n.classList.add("mdc-button__icon");
                    n.setAttribute("aria-hidden", "true");
                });
            }
        },
        reInstantiateRipple() {
            if (this.ripple) {
                if (this.mdcRipple) {
                    this.mdcRipple.destroy();
                }
                MDCRipple.attachTo(this.$el);
            } else {
                if (this.mdcRipple) {
                    this.mdcRipple.destroy();
                }
                this.mdcRipple = undefined;
            }
        }
    }
};
</script>
