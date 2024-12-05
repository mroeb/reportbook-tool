<template>
    <div style="padding: 1rem; width: 70%;">
        <v-btn @click="exportData" style="margin: 1rem;" variant="outlined" color="success">EXPORT</v-btn>
        <v-btn @click="resetAll" style="margin: 1rem;" variant="outlined" color="error">RESET</v-btn>
        <v-btn @click="showSettingsMenu" style="margin: 1rem;" variant="outlined" color="primary">SETTINGS</v-btn>
        <v-sheet :elevation="15" rounded style="padding: 1rem;">
            <v-btn @click="addTask" variant="outlined">ADD</v-btn>
            <div v-for="(item, index) in day" :key="index" draggable="true" @dragstart="dragStart(index, $event)" @dragover.prevent @drop="drop(index, $event)">
                <v-btn @click="toggleSaved(index)" variant="outlined" style="margin: 1rem;" color="success">{{ item.saved ? "✏️": "💾" }}</v-btn>
                <v-btn @click="deleteTask(index)" variant="outlined" style="margin: 1rem;" v-if="item.saved != true" color="error">REMOVE</v-btn>
                <div style="margin: 1rem;" v-if="item.saved == false">
                    <v-text-field v-model="item.heading" clearable variant="outlined" label="Heading"></v-text-field>

                    <div style="margin: 1rem;">
                        <div v-for="(a, i) in item.about" :key="i" draggable="true" @dragstart="dragStartAbout(index, i, $event)" @dragover.prevent @drop="dropAbout(index, i, $event)" style="display: flex;">
                            <v-text-field v-model="item.about[i]" clearable variant="outlined" label="About"></v-text-field>
                            <v-btn @click="removeAbout(index, i)" variant="outlined" style="margin: 1rem;">REMOVE</v-btn>
                        </div>
                        <v-btn @click="addAbout(index)" variant="outlined">ADD ABOUT</v-btn>
                    </div>
                </div>
                <div style="margin: 1rem;" v-if="item.saved == true">
                    <h3>{{ item.heading }}</h3>

                    <div style="margin: 1rem;">
                        <div v-for="(a, i) in item.about" :key="i" style="display: flex;">
                            <p>- {{ item.about[i] }}</p>
                        </div>
                    </div>
                </div>
            </div>
        </v-sheet>
        <v-dialog v-model="showExportDialog" max-width="600px">
            <v-card>
                <v-card-title class="headline">Exported Data</v-card-title>
                <v-card-text>
                    <v-textarea v-model="exportedData" rows="10" readonly></v-textarea>
                </v-card-text>
                <v-card-actions>
                    <v-spacer></v-spacer>
                    <v-btn color="primary" @click="showExportDialog = false">Close</v-btn>
                </v-card-actions>
            </v-card>
        </v-dialog>
        <SettingsMenu ref="settingsMenu" @save="saveDay" />
    </div>
</template>

<script>
import SettingsMenu from './SettingsMenu.vue';

export default {
    components: {
        SettingsMenu,
    },
    data: () => ({
        day: [],
        showExportDialog: false,
        exportedData: '',
        draggedTaskIndex: null,
        draggedAbout: { taskIndex: null, aboutIndex: null },
    }),
    methods: {
        addTask() {
            this.day.push({
                heading: "",
                about: [],
                saved: false,
            });
        },
        addAbout(index) {
            this.day[index].about.push("");
        },
        removeAbout(taskIndex, aboutIndex) {
            this.day[taskIndex].about.splice(aboutIndex, 1);
        },
        deleteTask(index) {
            this.day.splice(index, 1);
        },
        toggleSaved(index) {
            this.day[index].saved = !this.day[index].saved;
            this.saveDay();
        },
        resetAll() {
            this.day = [];
            localStorage.removeItem('day');
        },
        exportData() {
            const settings = this.$refs.settingsMenu.settings;
            let prompt = settings.customPrompt;
            if (settings.autoAddFeatures) {
                prompt += "\nverbessere den text";
            }
            this.exportedData = prompt + "\n\n" + JSON.stringify(this.day, null, 2);
            this.showExportDialog = true;
        },
        dragStart(index, event) {
            this.draggedTaskIndex = index;
            event.dataTransfer.effectAllowed = 'move';
        },
        drop(index, event) {
            const draggedItem = this.day[this.draggedTaskIndex];
            this.day.splice(this.draggedTaskIndex, 1);
            this.day.splice(index, 0, draggedItem);
            this.draggedTaskIndex = null;
            this.saveDay();
        },
        dragStartAbout(taskIndex, aboutIndex, event) {
            this.draggedAbout = { taskIndex, aboutIndex };
            event.dataTransfer.effectAllowed = 'move';
        },
        dropAbout(taskIndex, aboutIndex, event) {
            const draggedItem = this.day[this.draggedAbout.taskIndex].about[this.draggedAbout.aboutIndex];
            this.day[this.draggedAbout.taskIndex].about.splice(this.draggedAbout.aboutIndex, 1);
            this.day[taskIndex].about.splice(aboutIndex, 0, draggedItem);
            this.draggedAbout = { taskIndex: null, aboutIndex: null };
        },
        showSettingsMenu() {
            this.$refs.settingsMenu.showSettings = true;
        },
        handleKeyDown(event) {
            if (event.key === 'Escape') {
                this.showSettingsMenu();
            }
        },
        saveDay() {
            localStorage.setItem('day', JSON.stringify(this.day));
        },
    },
    mounted() {
        window.addEventListener('keydown', this.handleKeyDown);
        const savedDay = localStorage.getItem('day');
        if (savedDay) {
            this.day = JSON.parse(savedDay);
        }
    },
    beforeDestroy() {
        window.removeEventListener('keydown', this.handleKeyDown);
    },
}
</script>