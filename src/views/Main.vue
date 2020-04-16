<template>
  <div class="content">
    <div class="content-wrapper" :key="$route.fullPath">

      <div v-if="!workspaceLoaded">Workspace not loaded!</div>
      <div v-else>
        <!-- Image Panel -->
        <div class="image-panel">
          <div class="image-wrapper" :key="image.filename">
            <span class="body-2">{{ image.filename }}</span>
            <v-img id="target-image" :src="image.filepath"></v-img>
          </div>
        </div>

        <v-divider></v-divider>

        <!-- Input Panel -->
        <div class="input-panel">
          <v-container>
            <v-row justify="space-around">
              <v-col
                v-for="digit in digits"
                :key="digit.id"
                :id="`input-digit-`+digit.id"
                cols="12"
                md="3"
              >
                <v-sheet
                  class="pa-12"
                  color="grey lighten-3"
                >
                  <v-sheet
                    :elevation="6"
                    class="mx-auto digit-wrapper"
                    height="80"
                    width="60"
                  >
                    <span class="display-3">{{ digit.value }}</span>
                  </v-sheet>
                </v-sheet>
              </v-col>
            </v-row>
          </v-container>
        </div>

        <!-- Control Panel -->
        <v-bottom-navigation class="control-panel">
          <v-btn value="prev">
            <span>Prev</span>
            <v-icon>mdi-skip-previous-outline</v-icon>
          </v-btn>

          <v-btn class="non-clickable">
            <span>{{ image.now }} / {{ maxPage }}</span>
          </v-btn>

          <v-btn value="next" @clicked="nextImage">
            <span>Next</span>
            <v-icon>mdi-skip-next-outline</v-icon>
          </v-btn>
        </v-bottom-navigation>

      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'Main',
  data: () => ({
    workspaceLoaded: false,
    digits: [
      {
        id: 0,
        value: '1'
      },
      {
        id: 1,
        value: '3'
      },
      {
        id: 2,
        value: '6'
      },
      {
        id: 3,
        value: ''
      }
    ],
    image: {
      filepath: require('@/assets/test_imgs/1.jpg'),
      filename: '1.jpg',
      now: 1
    },
    maxPage: 100
  }),
  mounted () {
    this.checkWorkspaceLoaded()
  },
  methods: {
    checkWorkspaceLoaded () {
      const remote = require('electron').remote
      this.workspaceLoaded = remote.getGlobal('workspaceLoaded')
    },
    nextImage () {
      this.setCurrentImage(this.image.now + 1)
    },
    setCurrentImage (idx) {
      var vm = this

      const ipc = require('electron').ipcRenderer
      ipc.send('set-current-idx', idx)
      ipc.on('current-image-changed', function (event, curr) {
        console.log(curr)
        vm.image.now = curr.idx
        vm.image.filepath = curr.filepath
        vm.image.filename = curr.filename
      })
    }
  }
}
</script>

<style lang="scss" scoped>
@import "@/assets/scss/common.scss";

.image-panel {
  .image-wrapper {
    width: 240px;
    margin: 10px auto;

    #target-image {
      max-width: 240px;
    }
  }
}

.input-panel {
  .digit-wrapper {
    text-align: center;
    vertical-align: middle;
    display: table-cell;
  }
}

.control-panel {
  position: absolute;
  bottom: 0;
  padding-top: 5px;

  .non-clickable {
    cursor: default;
  }
}
</style>
