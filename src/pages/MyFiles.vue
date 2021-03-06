<template>
  <div class="container py-3">
    <action-bar 
      :selected-count="selectedItems.length" 
      @remove="handleRemove" 
      @rename="showModal = true"
      @files-choosen="choosenFiles= $event"
      />
    <div class="d-flex justify-content-between align-items-center py-2">

      <h6 class="text-muted mb-0">Files{{ selectedItems }}</h6>
      <sort-toggler @sort-change="handleSortChange($event)"/>
    </div>

    <teleport to="#search-form">
      <search-form  v-model="q"/>      
    </teleport>

    <drop-zone @files-dropped="choosenFiles = $event" :show-message="!files.length" >
      <files-list :files="files" @select-change="handleSelectChange($event)"/>
    </drop-zone>

    <app-toast 
      :show="toast.show" 
      :message="toast.message" 
      type="success" 
      position="bottom-left"
      @hide="toast.show = false"/>

      <app-modal 
        title="Rename" 
        :show="showModal && selectedItems.length === 1"
        @hide = "showModal = false">

        <file-rename-form 
          :file="selectedItems[0]" 
          @close="showModal = false "
          @file-updated="handleFileUpdated($event)"/>
      </app-modal>
      <uploader-popup :files="choosenFiles"/>
  </div>
</template>

<script>

import filesApi from "../api/files";
import { ref, reactive, watchEffect, toRef} from "vue";
import ActionBar from "../components/ActionBar.vue";
import SearchForm from '../components/SearchForm.vue';
import SortToggler from "../components/SortToggler.vue";
import FilesList from "../components/files/FilesList.vue";
import FileRenameForm  from "../components/files/FileRenameForm.vue";
import IconTypeCommon from '../components/icons/IconTypeCommon.vue';
import DropZone from "../components/uploader/file-chooser/DropZone.vue";
import UploaderPopup from "../components/uploader/popup/UploaderPopup.vue";

const fetchFiles = async(query) => {
    try{
      const { data } = await filesApi.index(query);
      return data;  
    }catch(error){  
      console.log(error);
    }
  };  

const removeItem = async(item, files) => {
  try {
    const response = await filesApi.delete(item.id);
    if (response.status === 200 || response.status === 204){
      const index = files.value.findIndex(file => file.id === item.id)
      files.value.splice(index, 1);
    }
  } catch (error) {
    console.error(error);
  }
}

export default {
  components: { 
    ActionBar, 
    IconTypeCommon, 
    FilesList, 
    SortToggler, 
    SearchForm, 
    FileRenameForm, 
    DropZone,
    UploaderPopup
    },
   setup(){
    const files = ref([]);

    const query = reactive({
      _sort: "name",
      _order: "asc",
      q: "",
    });

    const selectedItems = ref([]);

    const toast = reactive({
      show: false,
      message: ""
    })
    const showModal = ref(false);
    const choosenFiles = ref([]);

    const handleSelectChange = (items) => {
      selectedItems.value = Array.from(items)
    }

    const handleSortChange = (payload) => {
      query._sort = payload.column;
      query._order = payload.order;
    }  

    const handleRemove = () => {
      if(confirm("Are you sure?")) {
        selectedItems.value.forEach((item) => removeItem(item,files));
        selectedItems.value.splice(0);
        toast.show = true;
        toast.message = "Selected item successfully removed"
      }
    };

    const handleFileUpdated = (file) => {
      const oldFile =  selectedItems.value[0];
      const index = files.value.findIndex(item => item.id === file.id);
      files.value.splice(index, 1, file);
      toast.show = true;
      toast.message = `File '${oldFile.name}' to '${file.name}'`;
    }

    watchEffect(async() =>files.value = await fetchFiles(query));

    return { 
      files, 
      handleSortChange, 
      handleSelectChange, 
      selectedItems,
      handleRemove,
      toast,  
      showModal, 
      handleFileUpdated,
      choosenFiles,
      q: toRef(query, 'q') 
      }
  },
};
</script> 
 