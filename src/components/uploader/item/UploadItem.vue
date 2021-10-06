<template>
    <li 
        class="list-group-item d-flex justify-content-between align-items-center"> 
        <p class="upload-item">{{ item.file.name}}</p>
        <div class="upload-controls">X</div>
    </li>
</template>

<script>
export default {
    props: {
        item: {
            type:Object,
            required: true
        }
    },
}
</script>