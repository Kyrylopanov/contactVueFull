<template>
  <div id="app">
    <header>
		<nav class="menu">
        <div class="container-menuLeft">
            <a href="#">DASHBOARD</a>
            <a href="#">CONVERSATION</a>
            <a href="#">NOTIFICATION</a>
        </div>
            <div class="container-menuRight">
                <div class="dropdown hover">
                    <a href="#"><span class="userPic"></span>Jorah Mormont</a>
                      <ul>
                        <li><a href="#"><img src="./images/groups.png" alt="groups">Groups</a></li>
                        <li><a href="#"><img src="./images/fcont.png" alt="contact">Frequently contacted</a></li>
                        <li><a href="#"><img src="./images/pref.png" alt="preferences">Preferences</a></li>
                        <li><a href="#"><img src="./images/logout.png" alt="logout">Log out</a></li>
                      </ul>
                </div>
            </div>
		</nav>
        <div class="line-green"></div>
        <div class="container-contactify">
            <img src="./images/contacify.png" alt="contacify" class="person-select">
        </div>
        <div class="line-grey"></div>
        <div class="container-contactLeft">
            <input v-model="filterText" type="text" class="text-name" placeholder="Search Name">

            <div class="styled-select">
                    <select v-model="filterCity" class="select-city">
                        <option selected value="">select city</option>
                        <option value="Vilnius">Vilnius</option>
                        <option value="Kaunas">Kaunas</option>
                        <option value="Klaipėda">Klaipėda</option>
                        <option value="Tauragė">Tauragė</option>
                        <option value="Liepalingis">Liepalingis</option>
                    </select>
            </div>
        </div>
        <div class="container-contactRight">
            <form class="addContacts" v-on:submit.prevent="addContact">
              <input v-model="newContact.name" placeholder="Add New Name" class="contInput" type="text">
              <input v-model="newContact.surname" placeholder="Add New Surname" class="contInput" type="text">
              <input v-model="newContact.city" placeholder="Add New City" class="contInput" type="text">
              <input v-model="newContact.email" placeholder="Add New Email" class="contInput" type="text">
              <input v-model="newContact.phone" placeholder="Add New Phone Number" class="contInput" type="text">

              <input type="submit" class="container-btn2" value="Add New Contact">
            </form>
        </div>
	</header>
  <div class="form-container">
        <div class="form-main">
            <table>
             <thead>
                    <th @click="sortBy()" class="tableName imgSort">Name</th>
                    <th>Surname</th>
                    <th class="cityName" style="cursor:pointer">City</th>
                    <th>Email</th>
                    <th>Phone</th>
                    <th>Remove user</th>
             </thead>

               <transition-group name="fade" mode="out-in" tag="tbody" >
               <tr v-for="contact in filterByName" :key="contact">
                 <td class="tableText">{{contact.name}}</td>
                 <td class="tableText">{{contact.surname}}</td>
                 <td class="tableText">{{contact.city}}</td>
                 <td class="tableText">{{contact.email}}</td>
                 <td class="tableText">{{contact.phone}}</td>
                 <td @click="removeContact(contact)" class="tableText removeUser"></td>
               </tr>
               </transition-group>
            </table>
        </div>

	</div>

	<section id="contacts">
		<div class="contact-form">
			</div>
	</section>

	<footer id="cont">
        <div class="container-left">
            <div class="left-txt"><a href="#">Dashboard</a><a href="#">Conversations</a><a href="#">Conversations</a></div>
            <div class="left-info"><p>&copy 2015 Contactify</p><a href="#">About</a><a href="#">Privacy</a></div>
        </div>
        <div class="container-mid">
            <div class="cont-high">
                <div class="cloud">
                    <img src="./images/cloud.png" alt="cursor">
                </div>
                <div class="some-txt">Last synced<br>2015.06.02 14:33:10</div>
                <div class="sync-btn">
                    <button>FORCE SYNC</button>
                </div>
            </div>
            <div class="cont-low">
                 <div class="doc">
                    <img src="./images/doc.png" alt="doctor">
                </div>
                <div class="some-txt bottom">Help desc and Resolution center available<br>I-IV 08:00-18:00, V 08:00-16:45</div>
            </div>
        </div>
         <div class="container-right">
             <div class="left-txt"><a href="#">Groups</a><a href="#">Frequently contacted</a><a href="#">Preferences</a><a href="#">Log out</a></div>
        </div>


	</footer>

  </div>
</template>

<script>
import Hello from './components/Hello'

import Firebase from 'firebase'

let config = {
  apiKey: "AIzaSyCJqymWzat-mEz8_a1re65pouJoTLnZx7Y",
    authDomain: "contactify-1f140.firebaseapp.com",
    databaseURL: "https://contactify-1f140.firebaseio.com",
    projectId: "contactify-1f140",
    storageBucket: "contactify-1f140.appspot.com",
    messagingSenderId: "119115775861"
}

let app = Firebase.initializeApp(config);
let db = app.database();

let contactsRefs = db.ref('contacts');

export default {
  name: 'app',
  firebase: {
    contacts: contactsRefs
  },
  data() {
    return {
      newContact: {
        name: '',
        surname: '',
        city: '',
        email: '',
        phone: '',
        id: ''
      },
      filterText: '',
      filterCity: '',
      sortAsc: true
    }
  },
  methods: {
    addContact() {
      contactsRefs.push(this.newContact);
      this.newContact.name = '';
      this.newContact.surname = '';
      this.newContact.city = '';
      this.newContact.email = '';
      this.newContact.phone = '';
      this.newContact.id = '';
    },
    removeContact(contact) {
      contactsRefs.child(contact['.key']).remove();
      alert(contact.name + " " + contact.surname + " was removed");
    },
    sortBy(sortKey) {
      this.sortAsc = !this.sortAsc;
    }
  },
  computed: {
    filterByName() {
      let ascDesc = this.sortAsc ? 1 : -1;
      return this.contacts.sort((a, b) => ascDesc * a.name.localeCompare(b.name)).filter((element) => {
        if (this.filterText.length > 0 && this.filterCity.length > 0) {
          return element.name.toLowerCase().match(this.filterText.toLowerCase()) && element.city.toLowerCase().match(this.filterCity.toLowerCase());
        } else if (this.filterText.length === 0 && this.filterCity.length > 0) {
          return element.city.toLowerCase().match(this.filterCity.toLowerCase());
        } else {
          return element.name.toLowerCase().match(this.filterText.toLowerCase());
        }

      });

    }
  }
}
</script>

<style>

</style>
