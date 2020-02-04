<template>
    <textarea :value="content" ref="textareaElement"/>
</template>

<script lang="ts">
    import {Component, Vue, Prop} from 'vue-property-decorator';

    // @ts-ignore
    if (!jQuery.fn.summernote) {
        // @ts-ignore
        window.summer_note = require('summernote/dist/summernote-lite.js');
    }

    @Component({
        name: 'VueSummaryNoteLite',
    })
    export default class VueSummaryNoteLite extends Vue {
        // https://summernote.org/deep-dive/#initialization-options
        @Prop()
        public options: any;

        @Prop()
        public value?: string;

        @Prop({default: '', type: String})
        public placeholder?: string;

        public editor_option: any = {};

        public get content(): string {
            return this.value || ''
        }

        public onEvent(event: string, value: string = '') {
            if (event === 'onChange') {
                this.$emit('input', value);
                return;
            }
            this.$emit(event, value);
        }

        public mounted() {
            this.editor_option = {
                callbacks: {
                    onInit: () => this.onEvent('onInit'),
                    onEnter: () => this.onEvent('onEnter'),
                    onFocus: () => this.onEvent('onFocus'),
                    onBlur: () => this.onEvent('onBlur'),
                    onKeyup: () => this.onEvent('onKeyup'),
                    onKeydown: () => this.onEvent('onKeydown'),
                    onPaste: () => this.onEvent('onPaste'),
                    onImageUpload: () => this.onEvent('onImageUpload'),
                    onChange: (contents: string) => this.onEvent('onChange', contents),
                    onImageUploadError: () => this.onEvent('onImageUploadError'),
                },
                codeviewFilter: true,
                minHeight: '100px',
                ...(this.options || {})
            };

            this.run(this.editor_option);
        }

        public beforeDestroy() {
            this.run('destroy');
        }

        public run(command: any, option: any = undefined) {
            if (option !== undefined) {
                // @ts-ignore
                jQuery(this.$refs.textareaElement).summernote(command);
            } else {
                // @ts-ignore
                jQuery(this.$refs.textareaElement).summernote(command, option);
            }
        }
    }
</script>