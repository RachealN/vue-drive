<template>
    <div class="">
        <p class="text-info" v-show="!note">{{ message }}</p>
        <p> {{ note }}</p>
        <p class="text-danger"> {{ error }}</p>
        <i class="fa fa-microphone btn btn-danger" @click="toggleListening"></i>
    </div>
</template>
<script>
import useSpeechRecognition from "../../useSpeechRecognition";

export default {
     data: () => ({
        title: 'Speech to Text App',
        message:'Start Recording'
    }), 
    setup() {
        const { toggleListening, note, error } = useSpeechRecognition();

    return {
      toggleListening,
      note,
      error
    };
  },
}
</script>