<template>
    <div class="card shodow uploader-popup">
        <div class="card-header bg-dark py-3">
            <div class="d-flex justify-content-between align-items-center">
                <span class="text-light"> {{ uploadingStatus }} </span>
                <div class="popup-controls"> 
                    <button class="rounded-button me-2">
                        <icon-chevron-down />
                    </button>
                    <button>
                        <icon-times />
                    </button>
                </div>
            </div>
        </div>
        <div class="upload-items">
            <ul class="list-group list-group-flush">
                <li 
                    class="list-group-item d-flex justify-content-between align-items-center" 
                    v-for="item in items"
                    :key="`item-${item.id}`">
                    <p class="upload-item">{{ item.file.name}}</p>
                    <div class="upload-controls">
                        X
                    </div>

                </li>
            </ul>
        </div>
    </div>
</template>

<script>
import { computed, ref, watch} from 'vue';
import states from "../states";

export default {
    props: {
        files: {
            type: Object,
            required: true
        }
    }, 

    setup (props, {emit}) {
        const items = ref([]);
        
        const randomId = () => {
            return Math.random().toString(36).substr(2,9);
        }

        const getUploadItems = (files) =>{
            return Array.from(files).map(file => ({
                id: randomId(),
                file,
                progress: 0,
                state: states.WAITING,
                response: null
            }))
        }

        const uploadingItemsCount = computed(() => {
         return items.value.filter((item) => item.state === states.WAITING || item.state === states.UPLOADING).length; 
        })

        const uploadingStatus = computed(() => {
            return `Uploading ${uploadingItemsCount.value} items`;

        })
         
        watch(() => props.files, (newFiles) => {
            items.value.unshift(...getUploadItems(newFiles));
            
        })
       
        return { items, uploadingStatus };

    }
}
</script>

<style scoped>
.uploader-popup {
    width: 400px;
    position: fixed;
    bottom: 20px;
    right: 20px;
    z-index: 999 ;
}
</style>