<template>
    <div class="flex items-center">
        <div class="mr-2" v-if="!config.required">
            <toggle-fieldtype handle="enabled" v-model="enabled" />
        </div>
        <div class="w-48 mr-2">
            <v-select
                v-if="enabled"
                v-model="option"
                :options="options"
                :clearable="false"
                :reduce="(option) => option.value"
            />
        </div>
        <div class="flex-1">
            <text-input
                v-if="enabled && option === 'url'"
                :value="value"
                @input="update($event)" />

            <relationship-fieldtype
                v-if="enabled && option === 'entry'"
                ref="entries"
                handle="entry"
                :value="entriesValue"
                :config="meta.entry.config"
                :meta="meta.entry.meta"
                @input="entriesSelected"
                @meta-updated="meta.entry.meta = $event"
            />

        </div>
    </div>
</template>

<script>
export default {

    mixins: [Fieldtype],

    data() {
        return {
            enabled: this.value != null,
            option: 'url',
            options: [
                {label: 'URL', value: 'url'},
                {label: 'First Child', value: 'first-child'},
                {label: 'Entry', value: 'entry'}
            ],
            entriesValue: [],
        }
    },

    watch: {
        option(option, oldOption) {
            if (option === 'first-child') {
                this.update('@child');
            }

            if (oldOption === 'entry') {
                this.entriesValue = [];
                this.update(null);
            }

            if (option === 'entry') {
                setTimeout(() => this.$refs.entries.linkExistingItem(), 100);
            }
        },

        enabled(enabled) {
            if (enabled) {
                this.option = 'url';
            } else {
                this.option = null;
                this.update(null);
            }
        }
    },

    created() {
        if (this.value === '@child') {
            this.option = 'first-child';
        }

        if (this.value && this.value.startsWith('entry::')) {
            this.option = 'entry';
            this.entriesValue = [this.value.substr(7)];
        }

        if (this.config.required) {
            this.enabled = true;
        }
    },

    methods: {

        entriesSelected(entries) {
            this.entriesValue = entries;
            this.update(entries.length ? 'entry::' + entries[0] : null);
        }

    }

}
</script>
