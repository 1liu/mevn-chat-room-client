<template>
  <div>
    <button
      @click="clear"
      class="btn btn-primary my-3"
    >Toggle Message Form</button>
    <form v-if="showMessageForm" @submit.prevent="addMessage" class="my-3">
      <div v-if="error" class="alert alert-dismissible alert-danger">
        <button type="button" class="close" data-dismiss="alert">&times;</button>
        <h4 class="alert-heading">Error!</h4>
        <p class="mb-0">
          {{error}}
        </p>
      </div>
      <div class="form-group">
        <label for="username">Username</label>
        <input type="text" class="form-control" id="username" required v-model="message.username" />
      </div>
      <div class="form-group">
        <label for="subject">Subject</label>
        <input
          v-model="message.subject"
          type="text"
          class="form-control"
          id="subject"
          placeholder="Enter the subject"
          required
        />
      </div>
      <div class="form-group">
        <label for="message">Message</label>
        <textarea v-model="message.message" class="form-control" id="message" rows="3"></textarea>
      </div>
      <div class="form-group">
        <label for="imageUrl">Image URL</label>
        <input
          v-model="message.imageUrl"
          type="url"
          class="form-control"
          id="imageUrl"
          placeholder="Enter the Image URL"
        />
      </div>
      <button type="submit" class="btn btn-primary">Submit</button>
    </form>
    <div class="list-unstyled" v-for="message in reversedMessages" :key="message._id">
      <li class="media">
        <!-- <div class="whitespace mr-3" v-if="!message.imageUrl"></div> -->
        <img class="mr-3" v-if="message.imageUrl" :src="message.imageUrl" :alt="message.subject" />
        <div class="media-body">
          <h4 class="mt-0 mb-1">{{message.username}}</h4>
          <h5 class="mt-0 mb-1">{{message.subject}}</h5>
          {{message.message}}
          <br />
          <small>{{message.created}}</small>
        </div>
      </li>
      <hr />
    </div>
  </div>
</template>

<script>
// @ is an alias to /src
const API_URL = 'http://localhost:3003/messages';
export default {
  name: 'Home',
  data: () => ({
    error: '',
    showMessageForm: false,
    messages: [],
    message: {
      username: 'Anonymous',
      subject: '',
      message: '',
      imageUrl: '',
    },
  }),
  computed: {
    reversedMessages() {
      return this.messages.slice().reverse();
    },
  },
  mounted() {
    fetch(API_URL)
      .then((response) => response.json())
      .then((result) => {
        console.log(result);
        this.messages = result;
      });
  },
  methods: {
    addMessage() {
      // console.log(this.message);
      // remove imageUrl is empty string
      const msg = { ...this.message };
      if (!msg.imageUrl) {
        delete msg.imageUrl;
      }
      fetch(API_URL, {
        method: 'POST',
        body: JSON.stringify(msg),
        headers: {
          'content-type': 'application/json',
        },
      })
        .then((response) => response.json())
        .then((result) => {
          // console.log(result);
          if (result.details) {
            // there is error
            const error = result.details
              .map((detail) => detail.message)
              .join(' ');
            this.error = error;
            // console.log(error);
          } else {
            this.messages.push(result);
          }
        });
    },
    clear() {
      this.showMessageForm = !this.showMessageForm;
      this.error = '';
      this.message = {
        username: 'Anonymous',
        subject: '',
        message: '',
        imageUrl: '',
      };
    },
  },
};
</script>

<style>
hr {
  border: 1px solid white;
}

img {
  width: 300px;
  height: auto;
}

.whitespace{
 width: 300px;
}
</style>
