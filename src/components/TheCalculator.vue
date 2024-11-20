<script setup>
import { ref } from 'vue';

// Reactive state
const mortgageAmount = ref(null);
const mortgageInterest = ref(null);
const mortgageYear = ref(null);
const monthlyPayment = ref(null);
const totalRepayment = ref(null);

// Validation state
const mortgageTermError = ref(false);

// Validation function
const validateMortgageTerm = () => {
  mortgageTermError.value = !mortgageYear.value || mortgageYear.value < 1 || mortgageYear.value > 30;
};

// Form submission handler
const handleSubmit = (e) => {
  e.preventDefault();

  // Validate term
  validateMortgageTerm();

  if (!mortgageTermError.value) {
    calculate(); // Calculate if the input is valid
  }
};

// Monthly payment calculation
const calculate = () => {
  const principal = mortgageAmount.value;
  const annualInterest = mortgageInterest.value;
  const years = mortgageYear.value;

  const monthlyRate = annualInterest / 100 / 12;
  const totalPayments = years * 12;

  if (monthlyRate === 0) {
    monthlyPayment.value = (principal / totalPayments).toFixed(2);
  } else {
    monthlyPayment.value = (
      (principal * monthlyRate * Math.pow(1 + monthlyRate, totalPayments)) /
      (Math.pow(1 + monthlyRate, totalPayments) - 1)
    ).toFixed(2);
  }

  totalRepayment.value = (monthlyPayment.value * totalPayments).toFixed(2);

  
};
//clear all function
const clearAll = () => {
mortgageAmount.value = null;
mortgageInterest.value = null;
mortgageYear.value = null;
monthlyPayment.value = null;
totalRepayment.value = null;
mortgageTermError.value = false;
}

</script>


<template>
    <section>
      <article class="calcContainer">
        <div class="titleContainer">
          <h1 class="calcTitle">Mortgage Calculator</h1>
          <p @click="clearAll" class="clearAll">Clear All</p>
        </div>
        <form id="mortgageForm" @submit="handleSubmit" class="form">
          <label for="mortgageAmount">Mortgage Amount</label>
          <div class="inputWrapper">
            <p class="pound">£</p>
            <input
              v-model.number="mortgageAmount"
              type="number"
              id="mortgageAmount"
              name="mortgageAmount"
              class="inputWithIcon"
              required
            />
          </div>
  
          <div class="flexForm">
            <div class="termContainer">
              <label for="mortgageTerm">Mortgage Term</label>
              <div class="inputWrapperYears">
                <p class="years">years</p>
                <input
                  v-model.number="mortgageYear"
                  type="number"
                  id="mortgageTerm"
                  name="mortgageTerm"
                  :class="{ 'is-invalid': mortgageTermError }"
                  class="inputWithIcon"
                  required
                />
              </div>
            </div>
  
            <div class="interestContainer">
              <label for="interestRate">Interest Rate</label>
              <div class="inputWrapperInterest">
                <p class="interestIcon">%</p>
                <input
                  v-model.number="mortgageInterest"
                  type="number"
                  id="interestRate"
                  name="interestRate"
                  class="inputWithIcon"
                  required
                />
              </div>
            </div>
          </div>
  
          <label>Mortgage Type</label>
          <div class="radioFlexContainer">
            <div class="radioBtnContainer">
              <input
                type="radio"
                id="repayment"
                name="mortgageType"
                value="Repayment"
                required
              />
              <label for="repayment">Repayment</label>
            </div>
            <div class="radioBtnContainer">
              <input
                type="radio"
                id="interestOnly"
                name="mortgageType"
                value="Interest Only"
                required
              />
              <label for="interestOnly">Interest Only</label>
            </div>
          </div>
  
          <button type="submit">
            <i class="fa-solid fa-calculator"></i>Calculate Mortgage
          </button>
        </form>
      </article>
  
      <article class="resultsContainer">
            <!-- Only show the initial message if the monthly payment hasn't been calculated e.g === null -->
            <img v-show="monthlyPayment === null" src="../assets/images/illustration-empty.svg" alt="Calculator image" />
            <h2 v-show="monthlyPayment === null">Results Shown Here</h2>
            <p v-show="monthlyPayment === null">
                Complete the form and click "Calculate Mortgage" to see what your monthly
                repayments would be.
            </p>

            <!-- Conditional rendering for the dynamic results -->
            <transition name="fade">
              <!-- Tjekker, om variablen monthlyPayment har en værdi forskellig fra null. Sikrer at sektionen kun vises, når der er data til at udfylde resultatfeltet. -->
                <div v-if="monthlyPayment !== null" class="resultsDynamicContainer">
                    <h2>Your Results</h2>
                    <p class="resultText">Your results are shown below based on the information you provided. To adjust the results, edit the form and click "Calculate Mortgage" again.</p>
                    <div class="resultDiv">
                        <h3>Your monthly repayments</h3>
                        <h4 class="monthlyPayment">£{{ monthlyPayment }}</h4>
                        <h5 class="totalText">Total you'll repay over the term</h5>
                        <h2 class="totalResult">£{{ totalRepayment }}</h2>
                    </div>
                </div>
            </transition>
        </article>

    </section>
</template>
  
<style scoped>
.clearAll {
  transition: .3s ease-in-out;
}

.clearAll:hover {
  transform: scale(110%);
  cursor: pointer;
  color: var(--lime);
}

.fade-enter-active, .fade-leave-active {
  transition: opacity 0.5s ease;
}

.fade-enter, .fade-leave-to {
  opacity: 0;
}

.resultsDynamicContainer {
    text-align: left;
    opacity: 1;
}

.resultsDynamicContainer .resultText {
    font-size: 1rem;
}

.resultDiv {
    background-color: var(--slate900);
    padding: 1.5rem;
    border-radius: var(--borderRadius);
    border-top: 5px solid var(--lime);
}

.resultDiv h4 {
    font-size: 3rem;
    color: var(--lime);
    font-weight: 900;
    letter-spacing: .2rem;
    margin-bottom: var(--smallSpacing);
    padding-bottom: var(--smallSpacing);
    border-bottom: 1px solid var(--slate300);
}

.resultDiv h3, .resultDiv h5 {
    color: var(--slate300);
    margin-bottom: var(--smallSpacing);
    font-size: 1.2rem;
}

input.is-invalid {
  border-color: red;
  background-color: #ffe6e6;
}


 #mortgageForm .inputWrapper .inputWithIcon  {
    padding-left: 4rem;
}

.flexForm .inputWrapperYears input,
.flexForm .inputWrapperInterest input {
    padding-left: 1rem;
}

section {
    display: flex;
    position: absolute;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
    background-color: white;
    border-radius: 20px;
    box-shadow: 0px 10px 25px rgba(1, 86, 143, 0.161);
}
.resultsContainer {
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
    text-align: center;
    min-width: 35vw;
    background-color: var(--slate700);
    height: auto;
    padding: var(--largeSpacing);
    border-radius: var(--borderRadius);
    border-bottom-left-radius: 80px;
    border-top-left-radius: 0px;
    border-top-right-radius: 20px;
    border-bottom-right-radius: 20px;
    transition: opacity 0.5s ease-in-out;
}

.resultsContainer h2 {
    color: white;
    font-size: 1.5rem;
    margin-bottom: var(--smallSpacing);
    letter-spacing: .1rem;
}

.resultsContainer p {
    color: var(--slate300);
    font-size: .8rem;
}

.resultsContainer img {
    min-height: 45%;
}

.years, .interestIcon, .pound {
    font-weight: bold;
    background-color: var(--slate100);
    padding: .3rem 1rem;
    margin-right: -2%;
    border-radius: 5px;
    color: var(--slate700);
}

.pound {
    margin-left: -2%;
}


button {
    border-radius: 50px;
    background-color: var(--lime);
    transition: .4s ease-in-out;
    padding: 1rem;
    width: 100%;
    border: none;
    color: var(--slate900);
    font-weight: bold;
}

button:hover {
    background-color: var(--lightLime);
    transform: scale(105%);
    width: 100%;
}

button i {
    margin-right: var(--smallSpacing);
    font-size: 1.2rem;
}

label[for="repayment"],
label[for="interestOnly"] {
    margin-left: var(--smallSpacing);
}

.radioBtnContainer {
    padding: var(--smallSpacing);
    margin: var(--smallSpacing) 0;
    border-radius: 5px;
    border: 1px solid var(--slate300);
}

.inputWithIcon {
    width: 100%;
    padding: .5rem 0;
    border-radius: 5px;
    border: 1px solid var(--slate300);
    margin-top: .5rem;
}

.flexForm {
    display: flex;
    justify-content: space-between;
    align-items: center;
    gap: var(--smallSpacing);
    margin: var(--largeSpacing) 0;
}

form label {
    color: var(--slate700);
    font-size: 1rem;
}

.inputWrapper, .inputWrapperYears, .inputWrapperInterest {
    position: relative;
}

.inputWrapper i, .inputWrapper p {
    position: absolute;
    top: 60%;
    transform: translateY(-50%);
    left: 3%;
}

.inputWrapperYears p, .inputWrapperInterest p {
    position: absolute;
    top: 60%;
    transform: translateY(-50%);
    right: 3%;
}


.calcContainer {
    background-color: var(--white);
    min-width: 40vw;
    height: fit-content;
    padding: var(--largeSpacing);
    border-radius: 40px;
    border-top-right-radius: 0;
    border-bottom-right-radius: 0;
}

.titleContainer {
    display: flex;
    justify-content: space-between;
    align-items: baseline
}

.titleContainer h1 {
    font-weight: bold;
    color: var(--slate900);
    letter-spacing: .1rem;
    font-size: 1.2rem;
}

.titleContainer p {
    text-decoration: underline;
    color: var(--slate900);
    font-size: .8rem;
}

</style>