# radiobutton and checkbox with v-model

```
radio button과 checkbox는 name으로 grouping이 되는데, 각각의 element에 v-model의 값을 바인딩 해주면 된다.
radio-button과 check-box는 value의 값을 정의해줘야 하는데, 정의한 이 값들이 v-model에 초기화되게 된다.

check-box는 여러개를 클릭할 수 있기에, 초기화 값은 배열이 되며, radio-button은 한가지 값을 가지게 된다.
```

```vue
<template>
  <form @submit.prevent='submitForm'>
    <div class="form-control">
      <label for="user-name">Your Name</label>
      <input id="user-name" name="user-name" type="text" v-model='userName' />
    </div>
    <div class="form-control">
      <label for="age">Your Age (Years)</label>
      <input id="age" name="age" type="number" v-model='userAge'/>
    </div>
    <div class="form-control">
      <label for="referrer">How did you hear about us?</label>
      <select id="referrer" name="referrer" v-model='referrer'>
        <option value="google">Google</option>
        <option value="wom">Word of mouth</option>
        <option value="newspaper">Newspaper</option>
      </select>
    </div>
    <div class="form-control">
      <h2>What are you interested in?</h2>
      <div>
        <input id="interest-news" name="interest" type="checkbox" value="news" v-model='interest'/> //
        <label for="interest-news">News</label>
      </div>
      <div>
        <input id="interest-tutorials" name="interest" type="checkbox" value="tutorials" v-model='interest'/> //
        <label for="interest-tutorials">Tutorials</label>
      </div>
      <div>
        <input id="interest-nothing" name="interest" type="checkbox" value="nothing" v-model='interest'/> //
        <label for="interest-nothing">Nothing</label>
      </div>
    </div>
    <div class="form-control">
      <h2>How do you learn?</h2>
      <div>
        <input id="how-video" name="how" type="radio" v-model='how'/> //
        <label for="how-video">Video Courses</label>
      </div>
      <div>
        <input id="how-blogs" name="how" type="radio" v-model='how'/> //
        <label for="how-blogs">Blogs</label>
      </div>
      <div>
        <input id="how-other" name="how" type="radio" v-model='how'/> //
        <label for="how-other">Other</label>
      </div>
    </div>
    <div>
      <button>Save Data</button>
    </div>
  </form>
</template>

<script>
export default {
  data() {
    return {
      userName: '',
      userAge: null,
      referrer: 'wom',
      interest: [],
      how: null,

    };
  },
  methods: {
    submitForm() {
      console.log('UserName: ' + this.userName);
      this.userName = '';
      console.log("userAge: ");
      console.log(this.userAge);
      console.log(31);
      this.userAge = null;
      console.log("Referrer: " + this.referrer);
      this.referrer = "wom";
      console.log("checkboxes");
      console.log(this.interest);
      console.log("raido buttons");
      console.log(this.how);
      this.interest = null;
      this.how = null;
    }
  }
}
</script>
```

```
check-box가 여러개가 아닌 한개라면 이 태그는 click 했는지 안했는지에 대한 여부를 반환하며 v-model에 바인딩된다.
true / false
```

```vue
 <div class='form-control'>
      <input type='checkbox' id='confirm-terms' name='confirm-terms' v-model='confirm'/>
      <label for='confirm-terms'>Agree to terms of use?</label>
</div>


  data() {
    return {
      userName: '',
      userAge: null,
      referrer: 'wom',
      interest: [],
      how: null,
      confirm: false,
    };
  },
```

##### 출처: https://www.udemy.com/course/vuejs-2-the-complete-guide/learn/lecture
