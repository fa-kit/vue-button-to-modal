<template>
    <div 
        class="button-to-modal"
        ref="wrapper">
        <component
            v-bind:is="button"
            class="button-to-modal__button"
            :class="buttonClass"
            @click="opened = !opened"
            ref="button">
            <slot></slot>
        </component>
        <transition
            name="button-to-modal__close-button">
            <button 
                v-if="opened"
                @click="opened = !opened"
                class="button-to-modal__close-button"
                :class="modalBoxClass"
                :style="closeButtonStyles">
                <slot name="close">
                    <svg  
                        xmlns="http://www.w3.org/2000/svg" 
                        viewBox="0 0 474.75 600"
                        width="20"
                        height="20">
                        <polygon 
                            points="24.75 537.38 237.37 324.75 450 537.38 474.75 512.63 262.12 300 474.75 87.38 450 62.63 237.37 275.25 24.75 62.63 0 87.38 212.63 300 0 512.63 24.75 537.38"
                            fill="currentColor"/>
                    </svg>
                </slot>
            </button>
        </transition>
        <transition
            name="button-to-modal__modal-box"
            v-on:before-enter="calcBaseSize"
            v-on:enter="addOpenedClass"
            v-on:before-leave="removeOpenedClass"
            :duration="[8, 8]">
            <div 
                v-if="opened"
                class="button-to-modal__modal-box"
                :class="modalBoxClass"
                :style="modalStyles">
                <transition
                    name="button-to-modal__content"
                    appear>
                    <div 
                        class="button-to-modal__content"
                        :class="`${contentClass}`">
                        <slot name="content"></slot>
                    </div>
                </transition>
            </div>
        </transition>
    </div>
</template>

<script>
export default {
    name: 'ButtonToModal',
    props: {
        button: {
            type: String,
            default: 'button'
        },
        contentZIndex: {
            type: Number,
            default: 100
        },
        buttonClass: String,
        modalBoxClass: String,
        contentClass: String
    },
    data() {
        return {
            opened: false,
            modalStyles: {
                zIndex: this.contentZIndex,
            },
            closeButtonStyles: {
                zIndex: this.contentZIndex + 1,
            }
        }
    },
    mounted() {
        this.calcBaseSize();
    },
    methods: {
        calcBaseSize() {
            /**
             * Canculated position and size for content to make cool animation
             */
            this.modalStyles.transition = 'all .3s';
            this.modalStyles.left = this.$refs.button.getBoundingClientRect().left + 'px';
            this.modalStyles.top = this.$refs.button.getBoundingClientRect().top + 'px';
            this.modalStyles.height = this.$refs.button.clientHeight + 'px';
            this.modalStyles.width = this.$refs.button.clientWidth + 'px';
            console.log(this.modalStyles);
        },
        addOpenedClass(el) {
            /**
             * Set interval is needed to set up correct sequence of animation
             */
            setTimeout(()=>{
                el.classList.add('button-to-modal__modal-box--opened');
            }, 1)
        },
        removeOpenedClass(el) {
            /**
             * Method that minimize content
             */
            el.classList.remove('button-to-modal__modal-box--opened');
            this.calcBaseSize();
        }
    },
    watch: {
        opened() {
            /**
             * Logic to avoid scrolling when content is opened
             */
            if(this.opened) {
                document.body.style.overflow = 'hidden';
            } else {
                document.body.style.overflow = '';
            }
        }
    }
}
</script>

<style lang="sass">
$base-grid: 8px



.button-to-modal
    position: relative
    .button-to-modal__button
        max-width: 100%
        height: auto
        cursor: pointer
    .button-to-modal__modal-box
        position: fixed
        background-color: rgba(black, .95)
        overflow-y: auto
        // Transitions
        &-enter-active
            transition: all .3s .1s
            .button-to-modal__content
                visible: hidden
        &-leave-active
            .button-to-modal__content
                display: none
        // States
        &--opened
            left: 0!important
            top: 0!important
            height: 100vh!important
            width: 100vw!important
    .button-to-modal__close-button
        position: fixed
        top: 0
        right: 0
        background: transparent
            color: rgba(black, .9)
        border: none
        color: white
        width: $base-grid * 7
        height: $base-grid * 7
        cursor: pointer
        // Transitions
        &-enter-active
            transition: all .2s .3s
        &-leave-active
            transition: all .2s
        &-enter, &-leave-to
            transform: translate(100%, -100%)
    .button-to-modal__content
        background-color: white
        &-enter-active
            transition: all .3s .4s
        &-leave-active
            transition: all 1s
        &-enter
            opacity: 0
            transform: translateY(100px)
        &-leave-to
            opacoty: 0
</style>