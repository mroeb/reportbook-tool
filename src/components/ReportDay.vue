<template>
    <div style="padding: 1rem; width: 70%;">
        <v-btn @click="exportData" style="margin: 1rem;">EXPORT</v-btn>
        <v-sheet :elevation="15" rounded style="padding: 1rem;">
            <v-btn @click="addTask" variant="outlined">ADD</v-btn>
            <v-virtual-scroll :items="day" style="padding: 1rem;">
                <template v-slot:default="{ item, index }">
                    <v-btn @click="toggleSaved(index)" variant="outlined" style="margin: 1rem;">{{ item.saved ? "✏️": "💾" }}</v-btn>
                    <v-btn @click="deleteTask(index)" variant="outlined" style="margin: 1rem;" v-if="item.saved != true">REMOVE</v-btn>
                    <div style="margin: 1rem;" v-if="item.saved == false">
                        <v-text-field v-model="item.heading" clearable variant="outlined" label="Heading"></v-text-field>

                        <div style="margin: 1rem;">
                            <div v-for="(a, i) in item.about" :key="i" style="display: flex;">
                                <v-text-field v-model="item.about[i]" clearable variant="outlined" label="About"></v-text-field>
                                <v-btn @click="removeAbout(index, i)" variant="outlined" style="margin: 1rem;">REMOVE</v-btn>
                            </div>
                            <v-btn @click="addAbout(index)" variant="outlined">ADD ABOUT</v-btn>
                        </div>
                    </div>
                    <div style="margin: 1rem;" v-if="item.saved == true">
                        <h3 clearable variant="outlined" label="Heading">{{ item.heading }}</h3>

                        <div style="margin: 1rem;">
                            <div v-for="(a, i) in item.about" :key="i" style="display: flex;">
                                <p clearable variant="outlined" label="About">- {{ item.about[i] }}</p>
                            </div>
                        </div>
                    </div>
                </template>
            </v-virtual-scroll>
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
    </div>
</template>

<script>
export default {
    data: () => ({
        day: [],
        showExportDialog: false,
        exportedData: '',
    }),

    methods: {
        addTask() {
            this.day.push({
                icon: "",
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
        },
        
        exportData() {

            prompt = "Füge ein Icon hinzu und korrigiere Fehler. Formatiere im folgenden Format und nicht in JSON. (Konvertiere JSON zu Markdown-Text in einem Codeblock):\n# 💾 Überschrift \n- Nen Text\nHier sind die JSON-Daten: \n";

            this.exportedData = prompt + JSON.stringify(this.day, null, 2);
            this.showExportDialog = true;
        },
    },
}
</script>