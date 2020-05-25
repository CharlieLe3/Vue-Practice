<template>
  <div>
    <!-- Start of the form -->
    <button
      @click="showMessageForm = !showMessageForm"
      type="button"
      class="btn btn-info mt-3 mb-3">Toggle message form</button>
    <form class="mb-3" v-if="showMessageForm === true" @submit.prevent="addMessage">
      <div v-if="error" class="alert alert-danger" role="alert">
        Error! {{error}}
      </div>
      <div class="form-group">
        <label for="username">Username</label>
        <input
          v-model="message.username"
          type="text"
          class="form-control"
          id="username"
          reqiored
        />
      </div>
      <div class="form-group">
        <label for="subject">Subject</label>
        <input
          v-model="message.subject"
          type="text"
          class="form-control"
          id="subject"
          placeholder="Enter a subject"
          required
        />
      </div>
      <div class="form-group">
        <label for="message">Message</label>
        <textarea v-model="message.message" class="form-control" id="message" rows="3"></textarea>
      </div>
      <div class="form-group">
        <label for="imageURL">Image URL</label>
        <input
          v-model="message.imageURL"
          type="url"
          class="form-control"
          id="imageURL"
          placeholder="Enter an image URL"
        />
      </div>
      <button type="submit" class="btn btn-primary">Add Message</button>
    </form>
    <!-- End of the form -->
    <!-- Start of the List of Messages -->
    <div class="list-unstyled" v-for="message in reversedMessages" :key="message._id">
      <li class="media">
        <img v-if="message.imageURL" class="mr-3" :src="message.imageURL" :alt="message.subject" />
        <div class="media-body">
          <h4 class="mt-0 mb-1">{{message.username}}</h4>
          <h5 class="mt-0 mb-1">{{message.subject}}</h5>
          {{message.message}}
        </div>
      </li>
      <hr />
    </div>
    <!-- End of the List of Messages -->
  </div>
</template>

<script>
/* eslint-disable */
const API_URL = "https://pacific-mesa-80432.herokuapp.com/messages";

export default {
  name: "Home",
  data: () => ({
    showMessageForm: true,
    error: '',
    messages: [],
    message: {
      username: 'Anonymous',
      subject: '',
      message: '',
      imageURL: ''
    }
  }),
  mounted() {
    fetch(API_URL)
      .then(response => response.json())
      .then(result => {
        this.messages = result;
      });
  },
  computed: {
    reversedMessages() {
      return this.messages.slice().reverse();
    }
  },
  methods: {
    addMessage() {
      fetch(API_URL, {
        method: 'POST',
        // Server accepts json data so make the message from the
        // front end form into JSON format
        body: JSON.stringify(this.message),
        headers: {
          'content-type': 'application/json'
        }
      })
        .then((response) => response.json())
        .then((result) => {
          if (result.details) {
            const error = result.details.map(detail => detail.message).join('. ');
            this.error = error;
            console.log(error);
          } else {
            this.error = '';
            this.showMessageForm = false;
            this.messages.push(result);
          }
        });
    }
  },
};
</script>

<style scoped>
hr {
  border-top: 1px solid white;
}

img {
  max-width: 300px;
  height: auto;
}
</style>