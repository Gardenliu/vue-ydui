<template>
    <div>
        <yd-mask v-model="show" @click.native="close"></yd-mask>
        <div :class="classes" :style="styles" ref="box">
            <div v-if="!!$slots.top && position != 'center'" ref="top">
                <slot name="top"></slot>
            </div>
            <div class="yd-popup-content">
                <div ref="content">
                    <slot></slot>
                </div>
            </div>
            <div v-if="!!$slots.bottom && position != 'center'" ref="bottom">
                <slot name="bottom"></slot>
            </div>
        </div>
    </div>
</template>

<script type="text/babel">
    import {preventScroll} from '../../../utils/assist';
    import Mask from '../../mask/src/mask.vue';

    export default {
        name: 'yd-popup',
        components: {
            'yd-mask': Mask
        },
        data() {
            return {
                show: this.value
            }
        },
        props: {
            position: {
                validator(value) {
                    return ['bottom', 'center', 'left', 'right'].indexOf(value) > -1;
                },
                default: 'bottom'
            },
            height: {
                type: String,
                default: '50%'
            },
            width: {
                type: String,
                default: '50%'
            },
            value: {
                type: Boolean
            },
            closeOnMasker: {
                type: Boolean,
                default: true
            }
        },
        watch: {
            value(val) {
                val ? preventScroll.lock() : preventScroll.unlock();

                this.show = val;
            }
        },
        computed: {
            classes() {
                return (this.position === 'center' ? 'yd-popup-center ' : 'yd-popup yd-popup-' + this.position) +
                        (this.show ? ' yd-popup-show ' : '');
            },
            styles() {
                if (this.position === 'left' || this.position === 'right') {
                    return {width: this.width};
                } else if (this.position === 'bottom') {
                    return {width: '100%', height: this.height};
                } else {
                    return {width: this.width};
                }
            }
        },
        methods: {
            close() {
                if (this.closeOnMasker) {
                    this.show = false;
                    this.$emit('input', false);
                }
            }
        },
        destroyed() {
            preventScroll.unlock();
        }
    }
</script>

<style lang="less">
    @import '../../../styles/components/popup.less';
</style>
