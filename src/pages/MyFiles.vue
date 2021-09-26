<template>
  <div class="container py-3">
    <ActionBar />

    <div class="d-flex justify-content-between align-items-center py-2">
      <h6 class="text-muted mb-0">Files</h6>
      <sort-toggler @sort-change="handleSortChange($event)"/>
    </div>
    <teleport to="#search-form">
      <search-form  v-model="q"/>      
    </teleport>
    <files-list :files="files"/>
  </div>
</template>

<script>

import filesApi from "../api/files";
import { ref, reactive, watchEffect, toRef} from "vue"; 
import ActionBar from "../components/ActionBar.vue";
import SortToggler from "../components/SortToggler.vue";
import FilesList from "../components/files/FilesList.vue";
import IconTypeCommon from '../components/icons/IconTypeCommon.vue';
import SearchForm from '../components/SearchForm.vue';

const fetchFiles = async(query) => {
    try{
      const { data } = await filesApi.index(query);
      return data;  
    }catch(error){  
      console.log(error);
    }
  };  

export default {
  components: { ActionBar, IconTypeCommon, FilesList, SortToggler, SearchForm   },
  setup(){
    const files = ref([]);

    const query = reactive({
      _sort: "name",
      _order: "asc",
      q: ""
    });

    const handleSortChange = (payload) => {
      query._sort = payload.column;
      query._order = payload.order;
    }

    watchEffect(async() => files.value = await fetchFiles(query));
    
    return { files, handleSortChange, q: toRef(query, 'q') };

  },

  

  
  };
</script> 
 