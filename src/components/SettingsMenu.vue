<template>
    <v-dialog v-model="showSettings" max-width="600px">
        <v-card>
            <v-card-title class="headline">Settings</v-card-title>
            <v-card-text>
                <v-switch v-model="settings.autoAddFeatures" label="Automatically add features to prompt"></v-switch>
                <v-textarea v-model="settings.customPrompt" label="Custom Prompt"></v-textarea>
            </v-card-text>
            <v-card-actions>
                <v-spacer></v-spacer>
                <v-btn color="primary" @click="saveSettings">Save</v-btn>
                <v-btn color="secondary" @click="showSettings = false">Close</v-btn>
                <v-btn color="error" @click="clearSettings">Clear Settings</v-btn>
            </v-card-actions>
        </v-card>
    </v-dialog>
</template>

<script>
export default {
    data() {
        return {
            showSettings: false,
            settings: {
                autoAddFeatures: true,
                customPrompt: "Füge ein Icon hinzu und korrigiere Fehler. Formatiere im folgenden Format und nicht in JSON. (Konvertiere JSON zu Markdown-Text in einem Codeblock): \n# [Icon] [Überschrift] \n - [Text]\n Hier sind die JSON-Daten:\n ",
            },
        };
    },
    methods: {
        saveSettings() {
            localStorage.setItem('settings', JSON.stringify(this.settings));
            this.showSettings = false;
        },
        loadSettings() {
            const savedSettings = localStorage.getItem('settings');
            if (savedSettings) {
                this.settings = JSON.parse(savedSettings);
            }
        },
        clearSettings() {
            localStorage.removeItem('settings');
            this.settings = {
                autoAddFeatures: true,
                customPrompt: "Füge ein Icon hinzu und korrigiere Fehler. Formatiere im folgenden Format und nicht in JSON. (Konvertiere JSON zu Markdown-Text in einem Codeblock): \n# [Icon] [Überschrift] \n - [Text]\n Hier sind die JSON-Daten:\n ",
            };
        },
    },
    mounted() {
        this.loadSettings();
    },
};
</script>