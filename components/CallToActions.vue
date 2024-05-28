<template>
  <div class="CallToActions">
    <FormWithSocials
      v-if="showFormWithSocials"
      :popup-content="popupContent"
      :translate-lang="translateLang"
      @closePopup="openCloseFormWithSocials"
      @submit-success="handleSubmitSuccess"
    />
    <InputsPopup
      v-if="showInputsPopup"
      :translate-lang="translateLang"
      :page="inputsPopupPage"
      @closePopup="closeInputsPopup"
    />
    <div class="CallToActions__title">How can we serve you?</div>
    <div class="CallToActions__register" v-if="showSignUpMessage">
      <span class="CallToActions__registerText">
        Do you want to sign up to get weekly reminders via email? They contain
        special insights into the topic!
      </span>
      <a class="CallToActions__registerButton" @click="openRegisterPopup">
        Sign Up
      </a>
    </div>
    <div v-if="isUserLoggedIn" class="CallToActions__register">
      <a @click="logout" class="CallToActions__registerButton">Logout</a>
    </div>
    <!-- <RequestCoach
      class="CallToActions__RequestCoach"
      :translate-lang="translateLang"
      @openPopup="openCloseInputsPopup"
    /> -->
    <div class="CallToActions__Cards">
      <template v-for="(item, index) in items">
        <Card
          :key="`item-${index}`"
          class="CallToActions__Card"
          :index="index"
          v-bind="item"
          @click.native="openPopup(item)"
        />
      </template>
    </div>
  </div>
</template>

<script>
export default {
  name: "CallToActions",

  components: {
    Card: () => import("~/components/Card"),
    // RequestCoach: () => import("~/components/RequestCoach"),
    FormWithSocials: () => import("~/components/FormWithSocials"),
    InputsPopup: () => import("~/components/InputsPopup"),
  },

  props: {
    translateLang: {
      type: Array,
      default: () => [],
    },
    inputsPopupPage: {
      type: String,
      default: () => "reg",
    },
  },

  data() {
    return {
      isUserLoggedIn: false,
      popupContent: "",
      showFormWithSocials: false,
      showRequestCoach: false,
      showSignUpMessage: true,
      showInputsPopup: false,
      pray: {
        id: "prayRequest",
        icon: "pray-hands",
        type: "pray",
        title: "Pray for me",
        desc: "God loves to answer prayers! Leave your request and we will pray for you.",
        question: "Where do you want to receive your answer?",
        placeholder: "Please enter your prayer request",
        btnText: "Submit",
        hubspotId: "dbbaeef5-a7d6-4fa4-a68b-df392dd26bdd",
        showSocials: true,
      },
      bible: {
        id: "bibleStudy",
        hubspotId: "514e1b13-68b0-4cd6-8323-fdae0ff1c6eb",
        // Here is where I have to change the url to
        href: "http://more.bibleawr.com ",
      },
      question: {
        id: "askQuestion",
        icon: "answers",
        type: "question",
        title: "Ask a Bible question",
        desc: "Submit a Bible question and we will help you find the answer",
        question: "Where do you want to receive your answer?",
        placeholder: "Please enter your question",
        btnText: "Submit",
        hubspotId: "d4b36332-b4c7-400a-8d91-910b4a8fb037",
        showSocials: true,
      },
    };
  },

  computed: {
    items() {
      return [
        {
          id: "pray",
          icon: "pray-hands",
          title: "Pray for me",
          desc: "God loves to answer prayer. Leave your request and we will pray for you.",
        },
        {
          id: "bible",
          icon: "bible-closed",
          title: "Start Bible study",
          desc: "Find guidance for today in God's Word.",
        },
        {
          id: "question",
          icon: "answers",
          title: "Ask a Bible question",
          desc: "Submit a Bible question and we will help you find the answer",
        },
      ];
    },
  },
  mounted() {
    const { cta } = this.$route.query;

    switch (cta) {
      case "prayer_request":
        this.$emit("show-popup", this.pray);
        break;

      case "bible_question":
        this.$emit("show-popup", this.question);
        break;

      default:
        break;
    }
  },

  methods: {
    handleSubmitSuccess() {
      this.showSignUpMessage = false;
      this.isUserLoggedIn = true;
    },

    logout() {
      localStorage.removeItem("name");
      localStorage.removeItem("email");
      this.isUserLoggedIn = false;
      this.showSignUpMessage = true;
      this.$emit("user-logged-out");
    },

    openCloseFormWithSocials() {
      this.showFormWithSocials = !this.showFormWithSocials;
    },

    openPopup(item) {
      if (item.id === "pray") {
        this.popupContent = this.pray;
        this.showFormWithSocials = true;
      } else if (item.id === "bible") {
        window.open(this.bible.href, "_blank");
      } else if (item.id === "question") {
        this.popupContent = this.question;
        this.showFormWithSocials = true;
      }
    },
    openRegisterPopup() {
      this.showInputsPopup = true;
    },
    openCloseInputsPopup(status) {
      if (status === "reg") {
        this.showFormWithSocials = true;
      } else if (status === "success") {
        alert("Successfully registered!");
      }
      this.showRequestCoach = false;
    },
  },
};
</script>

<style scoped>
.CallToActions {
  max-width: 1150px;
  width: 100%;
  margin: 70px auto;
  padding: 0 20px;
}

.CallToActions__title {
  font-family: Kanit;
  padding: 0 30px;
  margin-bottom: 40px;
  line-height: 44px;
  font-style: normal;
  font-weight: 500;
  font-size: 40px;
  text-align: center;
  color: #0267b5;
}

.CallToActions__register {
  text-align: center;
  margin-bottom: 40px;
  display: flex;
  align-items: center;
  justify-content: center;
}

.CallToActions__registerText {
  font-family: Kanit;
  font-size: 18px;
  margin-right: 10px;
  color: #333;
}

.CallToActions__registerButton {
  color: #db4150;
  cursor: pointer;
  font-family: Kanit;
  font-weight: 500;
  font-size: 19px;
  line-height: 25px;
  white-space: nowrap;
}

.CallToActions__registerButton:hover {
  color: #d75d69;
}

.CallToActions__Cards {
  display: flex;
  flex-wrap: wrap;
}

.CallToActions__Card {
  margin-top: 20px;
}

.CallToActions__Card:not(:last-child) {
  margin-right: 20px;
}

@media (max-width: 824px) {
  .CallToActions__Card:not(:last-child) {
    margin-right: 0;
  }

  .CallToActions__Card:first-child {
    margin-right: 20px;
  }
}

@media (max-width: 549px) {
  .CallToActions__Card:first-child {
    margin-right: 0;
  }
}
</style>
