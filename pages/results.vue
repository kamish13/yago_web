<template>
  <div class="container">
    <h2>
      Thank you {{ name }}. Please see below the quote & recommendations. <br />
      Do you have any questions? Feel free to call us at 1-111-111-111
    </h2>
    <div v-if="recommendations">
      <h3>Our personalised recommendation based on your profile:</h3>
      <p>Deductible formula: {{ recommendations.deductible }}</p>
      <p>Coverage ceiling formula: {{ recommendations.coverageCeiling }}</p>
      <p>We recommend the following covers:</p>
      <!-- display recommended covers -->
      <li v-for="cover in recommendations.covers">
        {{ cover }}
      </li>
    </div>
    <div>
      <h3>Your personalized covers quote:</h3>
      <p>After Delivery: {{ grossPremiums.afterDelivery }}</p>
      <p>Public Liability: {{ grossPremiums.publicLiability }}</p>
      <p>
        Professional Indemnity:
        {{ grossPremiums.professionalIndemnity }}
      </p>
      <p>Entrusted Objects: {{ grossPremiums.entrustedObjects }}</p>
      <p>Legal Expenses: {{ grossPremiums.legalExpenses }}</p>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      name: "",
      recommendations: "",
      deductibles: "",
      grossPremiums: "",
    };
  },
  methods: {
    setQueryStringDefaults() {
      let params = new URLSearchParams(window.location.search);
      let name = params.get("name");
      let recommendations = params.get("recommendation");
      let grossPremiums = params.get("grossPremiums");

      this.name = name;
      this.recommendations = JSON.parse(recommendations);
      this.grossPremiums = JSON.parse(grossPremiums);

      return {
        name: name,
        recommendations: recommendations,
        grossPremiums: grossPremiums,
      };
    },
  },
  created() {
    this.setQueryStringDefaults();
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
</style>
