<template>
  <div>
    <NewMeetingForm :meetings="meetings" @added="addNewMeeting($event)"></NewMeetingForm>
    <span v-if="meetings.length == 0">Brak zaplanowanych spotkań.</span>
    <h3 v-else>Zaplanowane zajęcia ({{ meetings.length }})</h3>
    <MeetingsList :meetings="meetings" :username="username" @attend="addMeetingParticipant($event)"
      @unattend="removeMeetingParticipant($event)" @delete="deleteMeeting($event)"></MeetingsList>
  </div>
</template>

<script>
import NewMeetingForm from "./NewMeetingForm";
import MeetingsList from "./MeetingsList";
import axios from "axios";

export default {
  components: { NewMeetingForm, MeetingsList },
  props: {
    username: String,
  },
  data() {
    return {
      meetings: [],
      isError: false,
    };
  },
  created: async function () {
    let dataDb = [];
    let participantsDb = [];
    await axios
      .get("/api/meetings")
      .then((response) => {
        dataDb = response.data;
        console.log(dataDb);
        dataDb.forEach(async (element) => {
          axios
            .get("/api/meetings/" + element.id + "/participants")
            .then((response) => {
              participantsDb = response.data;
              (element.participants = []),
                participantsDb.forEach((elem) => {
                  element.participants.push(elem.login);
                }),
                this.meetings.push(element);
            })
            .catch(error =>
              console.log(
                error
              )
            );
        });
      })
      .catch(error => console.log(error));
  },
  methods: {
    addNewMeeting(meeting) {
      axios
        .post("/api/meetings", meeting)
        .then((response) => {
          meeting.id = response.data.id;
          console.log("Dodano spotkanie");
          this.meetings.push(meeting);
        })
        .catch(() => {
          console.log(
            error => console.log(error)
          );
        });
    },
    addMeetingParticipant(meeting) {
      axios
        .post("/api/meetings/" + meeting.id + "/participants", {
          login: this.username,
        })
        .then((response) =>
          console.log(
            "Dodano uczestnika"
          )
        )
        .catch(
          error => console.log(error)
        );
      this.meetings.at(this.meetings.indexOf(meeting)).participants.push(this.username);
    },
    removeMeetingParticipant(meeting) {
      axios
        .delete(
          "/api/meetings/" + meeting.id + "/participants/" + this.username
        )
        .then((response) =>
          console.log(
            "Usunięto uczestnika"
          )
        )
        .catch(
          error => console.log(error)
        );
      meeting.participants.splice(
        meeting.participants.indexOf(this.username),
        1
      );
    },
    deleteMeeting(meeting) {
      axios
        .delete("/api/meetings/" + meeting.id)
        .then((response) =>
          console.log("Usunięto spotkanie")
        )
        .catch(error => console.log(error));
      this.meetings.splice(this.meetings.indexOf(meeting), 1);
    },
  },
};
</script>

<style scoped>
.error {
  padding-top: -40px;
  padding-bottom: 20px;
  color: #f00;
}
</style>
