<template>
  <div class="container">
    <h1>New Contact</h1>
    <div>
      <div>
        First Name: <input v-model="newContactFirstName">
      </div>
      <div>
        Last Name: <input v-model="newContactLastName">
      </div>
      <div>
        Email: <input v-model="newContactEmail">
      </div>
      <div>
        Phone Number: <input v-model="newContactPhoneNumber">
      </div>
      <button v-on:click="createContact()">Create</button>
    </div>

    <h1>All Contacts</h1>
    <div v-for="contact in contacts">
      <h2>{{ contact.first_name }}</h2>
      <div>
      <button v-on:click="showContact(contact)">More Info</button>
      </div>
      <div v-if="contact === currentContact">
        <p>Last Name: {{ contact.last_name }}</p>
        <p>Email: {{ contact.email }}</p>
        <p>Phone Number: {{ contact.phone_number }}</p>
        <div>
          <h4>Edit Recipe</h4>
          <div>
            <div>
              First Name: <input v-model="contact.first_name">
            </div>
            <div>
              Last Name: <input v-model="contact.last_name">
            </div>
            <div>
              Email: <input v-model="contact.email">
            </div>
            <div>
              Phone Number: <input v-model="contact.phone_number">
            </div>
            <button v-on:click="updateContact(contact)">Update</button>
            <button v-on:click="destroyContact(contact)">Delete</button>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
var axios = require('axios');
export default {
  data: function() {
    return {
      contacts: [],
      currentContact: {},
      newContactFirstName: "",
      newContactLastName: "",
      newContactEmail: "",
      newContactPhoneNumber: ""
    };
  },
  created: function() {
    axios.get("/api/contacts")
      .then(response => {
        this.contacts = response.data;
    });
  },
  methods: {
    createContact: function() {
      var params = {
        first_name: this.newContactFirstName,
        last_name: this.newContactLastName,
        email: this.newContactEmail,
        phone_number: this.newContactPhoneNumber
      };
      axios.post("/api/contacts", params)
        .then(response => {
          console.log("Success", response.data);
          this.contacts.push(response.data);
        });
    },
    showContact: function(inputContact) {
      if (this.currentContact === inputContact) {
        this.currentContact = {};
      } else {
        this.currentContact = inputContact;
      }
    },
    updateContact: function(inputContact) {
      var params = {
                    first_name: inputContact.first_name,
                    last_name: inputContact.last_name,
                    email: inputContact.email,
                    phone_number: inputContact.phone_number
                  };
      axios.patch("/api/contacts/" + inputContact.id, params)
      .then(response => {
        console.log("Success", response.data);
        inputContact = response.data;
      });
    },
    destroyContact: function(inputContact) {
      axios.delete("/api/contacts/" + inputContact.id)
        .then(response => {
          console.log("Success", response.data);
          var index = this.contacts.indexOf(inputContact);
          this.contacts.splice(index,1);
        });
    }
  }
};
</script>
