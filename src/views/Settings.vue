<template>
  <div class="content">
    <div class="content-wrapper">

      <!-- Workspace Control -->
      <v-expansion-panels
        class="control-panel"
        v-model="panels[0]"
      >
        <v-expansion-panel>
          <v-expansion-panel-header>Workspace Control</v-expansion-panel-header>
          <v-expansion-panel-content>
            <span class="font-italic font-weight-light">{{ workspace }}</span>
            <div class="control-button-wrapper">
              <v-btn
                class="control-button"
                id="btn-workspace"
                small
                color="primary"
                @click="openWorkspace"
              >
                Open Workspace
              </v-btn>
            </div>
          </v-expansion-panel-content>
        </v-expansion-panel>
      </v-expansion-panels>

      <v-divider></v-divider>

      <!-- Output Control -->
      <v-expansion-panels
        class="control-panel"
        v-model="panels[1]"
      >
        <v-expansion-panel>
          <v-expansion-panel-header>Output File Control</v-expansion-panel-header>
          <v-expansion-panel-content>
            <v-text-field
              v-model="output"
              label="Output Filename"
              prepend-icon="mdi-file-delimited-outline"
            >
              {{ output }}
            </v-text-field>
            <div class="control-button-wrapper">
              <v-btn class="control-button" small color="primary">Save Filename</v-btn>
            </div>
          </v-expansion-panel-content>
        </v-expansion-panel>
      </v-expansion-panels>

    </div>
  </div>
</template>

<script>
export default {
  name: 'settings',
  data: () => ({
    panels: [0, 0],
    workspace: 'Please open your workspace',
    output: 'result.csv'
  }),
  methods: {
    openWorkspace () {
      var vm = this

      const ipc = require('electron').ipcRenderer

      ipc.send('open-file-dialog')
      ipc.on('selected-file', function (event, path) {
        vm.workspace = `${path}`
      })
    },
    saveOutputFilename () {
      console.log('output fielname')
    }
  }
}
</script>

<style lang="scss" scoped>
@import "@/assets/scss/common.scss";

.control-panel {
  margin: 10px 0;
}

.control-button-wrapper {
  float: right;
  .control-button {
    margin-right: 10px;
  }
}
</style>
