<template>
  <div class="container">
    <h1 class="title">RC Pro Quote Calculator</h1>
    <label>First Name</label>
    <input required type="text" v-model="clientInformation.firstName" />
    <br />
    <label>Last Name</label>
    <input required type="text" v-model="clientInformation.lastName" />
    <br />
    <label>Email</label>
    <input required type="email" v-model="clientInformation.email" />
    <br />
    <label>Phone</label>
    <input required type="text" v-model="clientInformation.phone" />
    <br />
    <label>Address</label>
    <input required type="text" v-model="clientInformation.address" />
    <br />
    <label>Annual Revenue</label>
    <input
      required
      type="number"
      v-model.number="quoteInformation.annualRevenue"
    />
    <br />
    <label>Enterprise Number</label>
    <input required type="text" v-model="quoteInformation.enterpriseNumber" />
    <br />
    <label>Legal Name</label>
    <input required type="text" v-model="quoteInformation.legalName" />
    <br />
    <label>Natural Person</label>
    <input type="checkbox" v-model="quoteInformation.naturalPerson" />
    <br />
    <label>Nacebel Codes</label>
    <input type="text" v-model="quoteInformation.nacebelCode" />
    <button type="button" @click="pushNacebelCode">Add</button>
    <!-- display nacebel codes -->
    <li v-for="nacebelCode in quoteInformation.nacebelCodes">
      {{ nacebelCode }}
    </li>
    <br />
    <button @click="calculateQuote">Calculate Quote</button>

    <div v-if="success == true">
      <h2>
        Thank you {{ clientInformation.firstName }}. Please see below the quote
        & recommendations. <br />
        Do you have any questions? Feel free to call us at 1-111-111-111
      </h2>
      <div v-if="results.recommendation">
        <h3>Our personalised recommendation based on your profile:</h3>
        <p>Deductible formula: {{ results.recommendation.deductible }}</p>
        <p>
          Coverage ceiling formula: {{ results.recommendation.coverageCeiling }}
        </p>
        <p>We recommend the following covers:</p>
        <!-- display recommended covers -->
        <li v-for="cover in results.recommendation.covers">
          {{ cover }}
        </li>
      </div>
      <div v-if="results">
        <h3>Your personalized covers quote:</h3>
        <p>After Delivery: {{ results.grossPremiums.afterDelivery }}</p>
        <p>Public Liability: {{ results.grossPremiums.publicLiability }}</p>
        <p>
          Professional Indemnity:
          {{ results.grossPremiums.professionalIndemnity }}
        </p>
        <p>Entrusted Objects: {{ results.grossPremiums.entrustedObjects }}</p>
        <p>Legal Expenses: {{ results.grossPremiums.legalExpenses }}</p>
      </div>
      <!-- reset button -->
      <button @click="reset">Reset</button>
      <p>
        Want to save your quote and check it later again? No problem, you can
        generate a link and access this quote anytime
      </p>
      <button @click="generateUrl">Generate URL</button>
      <br />
      <a :href="final_url" target="_blank">{{ final_url }}</a>
    </div>
    <div v-else-if="success == false && results">
      <h2>Something went wrong. Please try again</h2>
      <p>{{ results.message }}</p>
      <button @click="reset">Reset</button>
    </div>
  </div>
</template>

<script>
import fetch from "node-fetch";

export default {
  data() {
    return {
      clientInformation: {
        firstName: "",
        lastName: "",
        email: "",
        phone: "",
        address: "",
      },
      quoteInformation: {
        annualRevenue: 0,
        enterpriseNumber: "",
        legalName: "",
        naturalPerson: false,
        nacebelCode: "",
        nacebelCodes: [],
      },
      results: "",
      success: "",
      final_url: "",
    };
  },
  methods: {
    pushNacebelCode() {
      this.quoteInformation.nacebelCodes.push(
        this.quoteInformation.nacebelCode
      );
      this.quoteInformation.nacebelCode = "";
    },
    calculateQuote() {
      // check if data is not empty
      if (
        this.clientInformation.firstName == "" ||
        this.clientInformation.lastName == "" ||
        this.clientInformation.email == "" ||
        this.clientInformation.phone == "" ||
        this.clientInformation.address == "" ||
        this.quoteInformation.enterpriseNumber == "" ||
        this.quoteInformation.legalName == ""
      ) {
        alert("Please fill in all the fields");
      } else {
        fetch("https://yagoapi.herokuapp.com/quotes", {
          method: "POST",
          headers: {
            "Content-Type": "application/json",
          },
          body: JSON.stringify({
            annualRevenue: this.quoteInformation.annualRevenue,
            enterpriseNumber: this.quoteInformation.enterpriseNumber,
            legalName: this.quoteInformation.legalName,
            naturalPerson: this.quoteInformation.naturalPerson,
            nacebelCodes: this.quoteInformation.nacebelCodes,
          }),
        })
          .then((response) => response.json())
          .then((data) => {
            this.results = data.data;
            this.success = data.success;
          });
      }
    },
    reset() {
      (this.clientInformation = {
        firstName: "",
        lastName: "",
        email: "",
        phone: "",
        address: "",
      }),
        (this.quoteInformation = {
          annualRevenue: 0,
          enterpriseNumber: "",
          legalName: "",
          naturalPerson: false,
          nacebelCode: "",
          nacebelCodes: [],
        }),
        (this.results = "");
      this.success = "";
    },
    generateUrl() {
      var base_url = window.location.origin;
      var url = new URL(base_url + "/results");
      let params = {
        name: this.clientInformation.firstName,
        // recommendation: JSON.stringify(this.results.recommendation),
        grossPremiums: JSON.stringify(this.results.grossPremiums),
      };
      if (this.results.recommendation) {
        params = {
          ...params,
          recommendation: JSON.stringify(this.results.recommendation),
        };
      }
      url.search = new URLSearchParams(params).toString();
      // create final url
      var final_url = url.toString();
      this.final_url = final_url;
    },
  },
};
</script>

<style>
.container {
  margin: 0 auto;
  max-width: 800px;
  padding: 0 1rem;
  text-align: center;
}

label {
  display: inline-block;
  width: 140px;
  text-align: right;
  margin-bottom: 10px;
}

input {
  display: inline-block;
  width: 200px;
  text-align: left;
}
</style>
