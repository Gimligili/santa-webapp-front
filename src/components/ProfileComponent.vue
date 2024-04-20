<script setup>
  import { ref, onMounted, computed } from 'vue'
  import { useRoute, useRouter } from 'vue-router'
  import config from '@config/api_config.json';

  const route = useRoute()
  const router = useRouter()
  // Destructure the API IP and port from the configuration object
  const { ip, port } = config.api;
  const apiProfileUrl = `http://${ip}:${port}/api/profile/${route.params.id}`;
  const apiUpdateUrl = `http://${ip}:${port}/api/changeprofile/${route.params.id}`;
  const apiGetMyId = `http://${ip}:${port}/api/myprofile`;


  const username = ref("")
  const first_name = ref("")
  const last_name = ref("")
  const birthdate = ref("")
  const email_address = ref("")

  const readonly = ref(true)

  const loaded = ref(false)

  const myprofile = ref(false)

  const bdateDayInput = ref('')
  const bdateMonthInput = ref('')
  const bdateYearInput = ref('')

  const bdateCorrectYearInput = computed(() => {
    const year = new Date().getFullYear()
    return (Math.floor(bdateYearInput.value) === bdateYearInput.value) && (year - bdateYearInput.value < 118) && (year - bdateYearInput.value > 8)
  })

  const bdateCorrectMonthInput = computed(() => {
    return (Math.floor(bdateYearInput.value) === bdateYearInput.value) && (bdateMonthInput.value > 0) && (bdateMonthInput.value < 13)
  })

  const bdateCorrectDayInput = computed(() => {
    if (bdateDayInput.value <= 0) return false

    if (Math.floor(bdateYearInput.value) !== bdateYearInput.value) return false

    if (bdateMonthInput.value === 2) {
      return (!(bdateYearInput.value % 4) && (bdateYearInput.value % 100)) ? bdateDayInput.value < 29 : bdateDayInput.value < 30
    }
    if ((bdateMonthInput.value % 2 && bdateMonthInput.value < 8) || (!(bdateMonthInput.value % 2) && bdateMonthInput.value > 7)) {
      return bdateDayInput.value < 32
    } else {
      return bdateDayInput.value < 31
    }
  })

  const birthdateInput = computed(() => {
    return `${bdateDayInput.value}/${bdateMonthInput.value}/${bdateYearInput.value}`
  })

  function toggleDisplay () {
    readonly.value = !readonly.value
  }

  async function requestProfile (event) {
    await fetch(apiProfileUrl, {
      withCredntials: true,
      credentials: 'include',
      method: 'GET',
      headers: {
      'Accept': 'application/json',
      },
    })
    .then((response) => {
      response.json().then((data) => {
        username.value = data.login;
        first_name.value = data.first_name;
        last_name.value = data.surname;
        bdateDayInput.value = data.birthdate.split('/')[0];
        bdateMonthInput.value = data.birthdate.split('/')[1]
        bdateYearInput.value = data.birthdate.split('/')[2]
        email_address.value = data.email_address;
      });
    })
    .catch((error) => {
      router.push('/login')
    })

    await fetch(apiGetMyId, {
      withCredntials: true,
      credentials: 'include',
      method: 'GET',
      headers: {
      'Accept': 'application/json',
      },
    })
    .then((response) => {
      response.json().then((data) => {
        if (String(data.user_id) === route.params.id) {
          myprofile.value = true;
        }
        loaded.value = true;
      })
    })
    .catch((error) => {
      router.push('/login')
    })
  }

  onMounted(async () => {
    requestProfile();
  })

  function update_and_back_to_read (event) {
    requestUpdate();
  }

  function fetch_and_back_to_read (event) {
    toggleDisplay();
    requestProfile(event);
  }

  async function requestUpdate () {
  // event.preventDefault()
    
    await fetch(apiUpdateUrl, {
      withCredntials: true,
      credentials: 'include',
      method: 'PUT',
      headers: {
      'Content-Type': 'application/json',
      },
      dataType: 'json',
      body: JSON.stringify({
        first_name: first_name.value,
        surname: last_name.value,
        login: username.value,
        birthdate: birthdateInput.value,
        email_address: email_address.value // pass the URL to the backend
      })
    })
    .then(async (response) => {
      const reader = response.body.getReader()
      return reader.read().then(({ done, value }) => {
        if (done) {
          reader.cancel()
          return
        }
        return new TextDecoder('utf-8').decode(value)
      })
    })
    .then((stringResponse) => {
      console.log(stringResponse)
      if (stringResponse === "Updated Profile") {
        console.log('Profile Updated')
        toggleDisplay()
      }
    })
    .catch(err => console.error(err))
  }

</script>

<template>
<Transition>
  <form v-if="loaded" @submit.prevent="update_and_back_to_read">
    <fieldset v-if="readonly" >
      <legend>Profile</legend>
      <div class="grid-wrapper">
        <span>First Name</span> <span>{{first_name}}</span>
        <span>Last Name</span> <span>{{last_name}}</span>
        <span>Login</span> <span>{{username}}</span>
        <span v-show="bdateDayInput">Birthdate</span> <span v-show="bdateDayInput">{{bdateDayInput}}/{{bdateMonthInput}}/{{bdateYearInput}}</span>
        <span v-show="email_address">Email Address</span> <span v-show="email_address">{{email_address}}</span>
      </div>
      <button v-show="myprofile" @click="toggleDisplay" class="action">Modify</button>

    </fieldset>
    <fieldset v-else>
      <legend>Profile</legend>
      <div class="form-wrapper grid-wrapper" :class="{modify: myprofile}">
        <label for="first_name">First name*</label>
        <input required :class="{ wrong: !first_name }" autocomplete="off" placeholder="Enter your firstname" id="first_name" name="firstname" type="text" v-model="first_name">

        <label for="last_name">Last Name*</label>
        <input required :class="{ wrong: !last_name }" autocomplete="off" placeholder="Enter your surname" id="last_name" name="surname" type="text" v-model="last_name">

        <label for="username">Login*</label>
        <input required :class="{ wrong: !username }" autocomplete="off" placeholder="Enter a super login" id="username" name="login" type="text" v-model="username">
        
        <label for="email_address">Email*</label>
        <input required :class="{ wrong: !email_address }" autocomplete="off" placeholder="Enter your email" type="email" name="email" id="email_address" v-model="email_address">

        <label for="birthdateInput">Birthdate*</label>
          <div class="row-flex-wrapper">
            <input required v-model="bdateDayInput"   class="birthdate-input" :class="{ wrong: !bdateCorrectDayInput }"   placeholder="DD"   type="number" name="bdateDay"   id="bdateDayInput">
            <input required v-model="bdateMonthInput" class="birthdate-input" :class="{ wrong: !bdateCorrectMonthInput }" placeholder="MM"   type="number" name="bdateMonth" id="bdateMonthInput">
            <input required v-model="bdateYearInput"  class="birthdate-input" :class="{ wrong: !bdateCorrectYearInput }"  placeholder="YYYY" type="number" name="bdateYear"  id="bdateYearInput">
          </div>

      </div>
      
      <div class="row-flex-wrapper">
      <button type="submit">Update!</button>
      <button @click="fetch_and_back_to_read" class="cancel">Cancel</button>

      </div>


    </fieldset>
  </form>
  </Transition>
</template>

<style scoped>
  .grid-wrapper:not(.modify) {
    margin: 0.5em;
    border-radius: 0.3em;
    gap: 0;
  }
  
  .grid-wrapper > span {
    padding: 0.25em 0.8em;
  }

  .grid-wrapper > span:nth-child(2n):not(.modify) {
    justify-self: end;
  }

  nav {
    display: flex;
    flex-direction: column;
    align-items: center;
  }

  nav > a {
    padding: 0.1em 0.5em;
    border-radius: 0.5em;
  }

  a.forgot-password-link {
    color: crimson;
  }

  a.forgot-password-link:hover {
    background-color: rgba(220, 20, 60, 0.1);
  }

  .v-enter-active,
  .v-leave-active {
    transition: opacity 0.5s ease;
  }

  .v-enter-from,
  .v-leave-to {
    opacity: 0;
  }
</style>