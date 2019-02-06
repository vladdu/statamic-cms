<template>
    <div class="wysiwyg-fieldtype-wrapper">
        <ckeditor :editor="editor" @input="update" v-model="editorData" :config="editorConfig"></ckeditor>
    </div>
</template>

<script>

import CKEditor from '@ckeditor/ckeditor5-vue';
import ClassicEditor from '@ckeditor/ckeditor5-build-classic';

export default {

    mixins: [Fieldtype],

    components: {
        ckeditor: CKEditor.component
    },

    data: function() {
        return {
            editor: ClassicEditor,
            editorData: this.value,
            editorConfig: {
                // config goes up in hurrr
            }
        }
    },

    methods: {
        update() {
            this.$emit('updated', this.editorData);
        },
    },
};
</script>
