<template>
  <div class="container py-3">
    <ActionBar />

    <div class="d-flex justify-content-between align-items-center py-2">
      <h6 class="text-muted mb-0">Files</h6>
      <button class="rounded-button">
        <icon-arrow-up />
      </button>
    </div>
    <div class="row">
      <div class="col-md-3" v-for="file in files" :key="`file=${file.id}`">
        <div class="card mb-4">
          <img class="file-thumb" :src="file.url" v-if="file.url" />
          <div class="card-body text-center py-5" v-else>
            <icon-type-common height="4em" width="4em" />
          </div>
          <div class="card-footer">
            <div class="d-flex align-items-center">
              <icon-type-common />
              <span class="file-name">{{file.name}}</span>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import filesApi from "../api/files";
import ActionBar from "../components/ActionBar.vue";
import IconTypeCommon from '../components/icons/IconTypeCommon.vue';

export default {
  components: { ActionBar, IconTypeCommon },

  mounted(){
    this.fetchFiles()
  },

  data: () => ({
    files: []
  }),

  methods: {
    async fetchFiles() {
      try{
        const { data } = await filesApi.index();
        this.files = data;  
      }catch(error){  
        console.log(error);
      }
    }

  }
};
</script>
