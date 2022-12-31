# adding form validation

```
coach form에 입력 validate를 추가한다.
```

```vue
//CoachForm.vue

<template>
  <form @submit.prevent='submitForm'>
    <div class='form-control' :class='{invalid: !firstName.isValid}'>
      <label for='firstname'>Firstname</label>
      <input type='text' id='firstname' v-model.trim='firstName.val'/>
      <p v-if='!firstName.isValid'>Firstname must not be empty</p>
    </div>
    <div class='form-control' :class='{invalid: !lastName.isValid}'>
      <label for='lastname'>LastName</label>
      <input type='text' id='lastname' v-model.trim='lastName.val'/>
      <p v-if='!lastName.isValid'>LastName must not be empty</p>
    </div>
    <div class='form-control' :class='{invalid: !description.isValid}'>
      <label for='description'>Description</label>
      <textarea id='description' rows='5' v-model.trim='description.val'/>
      <p v-if='!description.isValid'>Description must not be empty</p>
    </div>
    <div class='form-control' :class='{invalid: !rate.isValid}'>
      <label for='rate'>Hourly Rate</label>
      <input type='number' id='rate' v-model.number='rate.val'/>
      <p v-if='!rate.isValid'>Rate must not be empty</p>
    </div>
    <div class='form-control' :class='{invalid: !areas.isValid}'>
      <h3>Areas of Expertise</h3>
      <div>
        <input type='checkbox' id='frontend' value='frontend' v-model='areas.val'>
        <label for='frontend'>Frontend Development</label>
        <div>
          <input type='checkbox' id='backend' value='backend' v-model='areas.val'>
          <label for='backend'>Backend Development</label>
        </div>
        <div>
          <input type='checkbox' id='career' value='career' v-model='areas.val'>
          <label for='career'>Career Advsory</label>
        </div>
        <p v-if='!areas.isValid'>At least one expertise must be selected</p>
      </div>
    </div>
    <p v-if='!formIsValid'>Please fix the above errors and submit again.</p>
      <base-button>Register</base-button>
  </form>
</template>

<script>
export default {
  emits: ['save-data'],
  name: 'CoachForm',
  data() {
    return {
      firstName: {
        val: '',
        isValid: true,
      },
      lastName:  {
        val: '',
        isValid: true,
      },
      description:  {
        val: '',
        isValid: true,
      },
      rate:  {
        val: null,
        isValid: true,
      },
      areas:  {
        val: [],
        isValid: true,
      },
      formIsValid: true,
    };
  },
  methods: {
    validateForm() {
      this.formIsValid = true;
      if (this.firstName.val === '') {
        this.firstName.isValid = false;
        this.formIsValid = false;
      }
      if (this.lastName.val === '') {
        this.lastName.isValid = false;
        this.formIsValid = false;
      }
      if (this.description.val === '') {
        this.description.isValid = false;
        this.formIsValid = false;
      }
      if (!this.rate.val || this.rate.val < 0) {
        this.rate.isValid = false;
        this.formIsValid = false;
      }
      if (this.areas.val.length <= 0) {
        this.areas.isValid = false;
        this.formIsValid = false;
      }
    },

    submitForm() {
      this.validateForm();

      if (!this.formIsValid) {
        return;
      }

      const formData = {
        first: this.firstName,
        last: this.lastName,
        desc: this.description,
        rate: this.rate,
        areas: this.areas
      };

      this.$emit('save-data', formData);
    }
  }
};
</script>

<style scoped>
.form-control {
  margin: 0.5rem 0;
}

label {
  font-weight: bold;
  display: block;
  margin-bottom: 0.5rem;
}

input[type='checkbox'] + label {
  font-weight: normal;
  display: inline;
  margin: 0 0 0 0.5rem;
}

input,
textarea {
  display: block;
  width: 100%;
  border: 1px solid #ccc;
  font: inherit;
}

input:focus,
textarea:focus {
  background-color: #f0e6fd;
  outline: none;
  border-color: #3d008d;
}

input[type='checkbox'] {
  display: inline;
  width: auto;
  border: none;
}

input[type='checkbox']:focus {
  outline: #3d008d solid 1px;
}

h3 {
  margin: 0.5rem 0;
  font-size: 1rem;
}

.invalid label {
  color: red;
}

.invalid input,
.invalid textarea {
  border: 1px solid red;
}
</style>
```

```
form 에서 button 클릭 전 validate 메소드를 호출하고 해당 메소드에서 데이터들에 대한 validate 를 check 한다.
이전 data와는 달리 값만 있던 것에서 object 로 변경되었다. object 내부에는 value와 isValid 속성을 갖게 된다.

아직 남아있는 처리중에 하나가 있는데, form 전송 시 입력되지 않은 곳에 빨간색으로 알려지는데, 다시 입력을 하고 button을
누르기 전까지 남아있게 된다. 이러한 ux를 해결하기 위해 input에 blur 시 event를 발생시켜 처리한다.
```

```vue
//CoachForm.vue

<template>
  <form @submit.prevent='submitForm'>
    <div class='form-control' :class='{invalid: !firstName.isValid}'>
      <label for='firstname'>Firstname</label>
      <input type='text' id='firstname' v-model.trim='firstName.val' @blur='clearValidity("firstName")'/>   //
      <p v-if='!firstName.isValid'>Firstname must not be empty</p>
    </div>
    <div class='form-control' :class='{invalid: !lastName.isValid}'>
      <label for='lastname'>LastName</label>
      <input type='text' id='lastname' v-model.trim='lastName.val' @blur='clearValidity("lastName")'/>   //
      <p v-if='!lastName.isValid'>LastName must not be empty</p>
    </div>
    <div class='form-control' :class='{invalid: !description.isValid}'>
      <label for='description'>Description</label>
      <textarea id='description' rows='5' v-model.trim='description.val' @blur='clearValidity("description")'/>   //
      <p v-if='!description.isValid'>Description must not be empty</p>
    </div>
    <div class='form-control' :class='{invalid: !rate.isValid}'>
      <label for='rate'>Hourly Rate</label>
      <input type='number' id='rate' v-model.number='rate.val' @blur='clearValidity("rate")'/>   //
      <p v-if='!rate.isValid'>Rate must not be empty</p>
    </div>
    <div class='form-control' :class='{invalid: !areas.isValid}'>
      <h3>Areas of Expertise</h3>
      <div>
        <input type='checkbox' id='frontend' value='frontend' v-model='areas.val' @blur='clearValidity("areas")'>   //
        <label for='frontend'>Frontend Development</label>
        <div>
          <input type='checkbox' id='backend' value='backend' v-model='areas.val'  @blur='clearValidity("areas")'>   //
          <label for='backend'>Backend Development</label>
        </div>
        <div>
          <input type='checkbox' id='career' value='career' v-model='areas.val'  @blur='clearValidity("areas")'>   //
          <label for='career'>Career Advsory</label>
        </div>
        <p v-if='!areas.isValid'>At least one expertise must be selected</p>
      </div>
    </div>
    <p v-if='!formIsValid'>Please fix the above errors and submit again.</p>
      <base-button>Register</base-button>
  </form>
</template>

<script>
export default {
  emits: ['save-data'],
  name: 'CoachForm',
  data() {
    return {
      firstName: {
        val: '',
        isValid: true,
      },
      lastName:  {
        val: '',
        isValid: true,
      },
      description:  {
        val: '',
        isValid: true,
      },
      rate:  {
        val: null,
        isValid: true,
      },
      areas:  {
        val: [],
        isValid: true,
      },
      formIsValid: true,
    };
  },
  methods: {
    clearValidity(input) {           //
      this[input].isValid = true;    //
    }, 
    validateForm() {
      this.formIsValid = true;
      if (this.firstName.val === '') {
        this.firstName.isValid = false;
        this.formIsValid = false;
      }
      if (this.lastName.val === '') {
        this.lastName.isValid = false;
        this.formIsValid = false;
      }
      if (this.description.val === '') {
        this.description.isValid = false;
        this.formIsValid = false;
      }
      if (!this.rate.val || this.rate.val < 0) {
        this.rate.isValid = false;
        this.formIsValid = false;
      }
      if (this.areas.val.length <= 0) {
        this.areas.isValid = false;
        this.formIsValid = false;
      }
    },

    submitForm() {
      this.validateForm();

      if (!this.formIsValid) {
        return;
      }

      const formData = {                  //
        first: this.firstName.val,
        last: this.lastName.val,
        desc: this.description.val,
        rate: this.rate.val,
        areas: this.areas.val                 //
      };

      this.$emit('save-data', formData);
    }
  }
};
</script>

<style scoped>
.form-control {
  margin: 0.5rem 0;
}

label {
  font-weight: bold;
  display: block;
  margin-bottom: 0.5rem;
}

input[type='checkbox'] + label {
  font-weight: normal;
  display: inline;
  margin: 0 0 0 0.5rem;
}

input,
textarea {
  display: block;
  width: 100%;
  border: 1px solid #ccc;
  font: inherit;
}

input:focus,
textarea:focus {
  background-color: #f0e6fd;
  outline: none;
  border-color: #3d008d;
}

input[type='checkbox'] {
  display: inline;
  width: auto;
  border: none;
}

input[type='checkbox']:focus {
  outline: #3d008d solid 1px;
}

h3 {
  margin: 0.5rem 0;
  font-size: 1rem;
}

.invalid label {
  color: red;
}

.invalid input,
.invalid textarea {
  border: 1px solid red;
}
</style>

```

##### 출처: https://www.udemy.com/course/vuejs-2-the-complete-guide/learn/lecture/21879948#overview
