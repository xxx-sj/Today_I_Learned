# working on the contact form

```
contact coach 페이지를 만들기
coach에게 form을 이용해 message및 전달받을 email을 작성하는 component이다.
```

```vue
//ContactCoach.vue

<template>
  <form @submit.prevent='submitForm'>
    <div class='form-control'>
      <label for='email'>Your E-Mail</label>
      <input type='email' id='email' v-model.trim='email'/>
    </div>
    <div>
      <label for='message'>Message</label>
      <textarea rows='5' id='message' v-model.trim='message'/>
    </div>
    <p class='errors' v-if='!formIsValid'>Please enter a valid email and non-empty message.</p>
    <div class='actions'>
      <base-button>Send Message</base-button>
    </div>
  </form>
</template>

<script>
export default {
  name: 'ContactCoach',
  data() {
    return {
      email: '',
      message: '',
      formIsValid: true,
    };
  },
  methods: {
    submitForm() {
      this.formIsValid = true;
      if (this.email === '' || !this.email.includes("@") || this.message === '') {
        this.formIsValid = false;
        return;
      }
    },
  },
};
</script>

<style scoped>
form {
  margin: 1rem;
  border: 1px solid #ccc;
  border-radius: 12px;
  padding: 1rem;
}

.form-control {
  margin: 0.5rem 0;
}

label {
  font-weight: bold;
  margin-bottom: 0.5rem;
  display: block;
}

input,
textarea {
  display: block;
  width: 100%;
  font: inherit;
  border: 1px solid #ccc;
  padding: 0.15rem;
}

input:focus,
textarea:focus {
  border-color: #3d008d;
  background-color: #faf6ff;
  outline: none;
}

.errors {
  font-weight: bold;
  color: red;
}

.actions {
  text-align: center;
}
</style>

```

##### 출처: https://www.udemy.com/course/vuejs-2-the-complete-guide/learn/lecture/21879954#content
