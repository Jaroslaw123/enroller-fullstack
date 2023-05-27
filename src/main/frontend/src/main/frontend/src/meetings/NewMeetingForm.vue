<template>
  <form @submit.prevent="addNewMeeting()">
    <h3>Dodaj nowe spotkanie</h3>
    <label>Nazwa</label>
    <input type="text" v-model="newMeeting.title">
    <label>Opis</label>
    <textarea v-model="newMeeting.description"></textarea>
    <button>Dodaj</button>
    <span class="error" v-if="error">Spotkanie musi mieć nazwę!</span>
    <!-- <span class="error" v-if="missingTitleError">Spotkanie musi mieć nazwę!</span> -->
    <!-- <span class="error" v-if="duplicatedTitleError">Spotkanie o podanej nazwie istnieje. Wprowadź inną nazwę.</span> -->
  </form>
</template>

<script>
export default {
  props: {
    meetings: Array
  },
  data() {
    return {
      newMeeting: {participants: []},
      error: false,
      // missingTitleError: false,
      // duplicatedTitleError: false
    };
  },
  methods: {
    async addNewMeeting() {
      let meetingId;
      this.error = false;
      if (this.newMeeting.title) {
        this.$emit('added', this.newMeeting);
        this.newMeeting = {participants: []};
      } else {
        this.error = true;
      }
    }
  }
}
</script>

<style scoped>
.error {
  padding-left: 30px;
  text-align: center;
  color: #F00;
}
</style>