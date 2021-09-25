<template>
  <div class="container py-3">
    <ActionBar />

    <div class="d-flex justify-content-between align-items-center py-2">
      <h6 class="text-muted mb-0">Files</h6>
      <sort-toggler />
    </div>
    <files-list :files="files"/>
  </div>
</template>

<script>

import filesApi from "../api/files";
import { ref , onMounted} from "vue";
import ActionBar from "../components/ActionBar.vue";
import SortToggler from "../components/SortToggler.vue";
import FilesList from "../components/files/FilesList.vue";
import IconTypeCommon from '../components/icons/IconTypeCommon.vue';

const fetchFiles = async() => {
    try{
      const { data } = await filesApi.index();
      return data;  
    }catch(error){  
      console.log(error);
    }
  };  

export default {
  components: { ActionBar, IconTypeCommon, FilesList, SortToggler   },
  setup(){
    const files = ref([]);

    onMounted(async() =>files.value = await fetchFiles());

    return { files };

  },

  };
</script> 
