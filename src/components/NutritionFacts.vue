<template>
  <div class="nutrition-facts">
    <div class="title">Nutrition Facts</div>
    
    <div class="serving-info">
      <div class="serving-size">
        <span>Serving Size</span>
        <input v-model="servingSize" type="text" placeholder="228" :readonly="baseValuesSet" />
        <span>g</span>
      </div>
      <div class="servings-per-container">
        <span>Total Amount in Container</span>
        <input v-model="servingsPerContainer" type="text" placeholder="456" :readonly="baseValuesSet" />
        <span>g</span>
      </div>
    </div>

    <div class="thick-line"></div>

    <div class="calories-section">
      <div class="amount-per-serving">Amount Per Serving</div>
      <div class="calories-row">
        <span class="calories-label">Calories</span>
        <input v-model="calories" type="text" placeholder="250" class="calories-input" :readonly="baseValuesSet" />
      </div>
    </div>

    <div class="medium-line"></div>

    <div class="nutrients-section">
      <div class="nutrient-row">
        <span class="nutrient-label">Total Fat</span>
        <div class="nutrient-amount">
          <input v-model="totalFat" type="text" placeholder="12" @input="recalculateValues" />
          <span>g</span>
        </div>
      </div>
      
      <div class="nutrient-row sub-nutrient">
        <span class="nutrient-label sub-label">Saturated Fat</span>
        <div class="nutrient-amount">
          <input v-model="saturatedFat" type="text" placeholder="3" />
          <span>g</span>
        </div>
      </div>
      
      <div class="nutrient-row">
        <span class="nutrient-label sub-label">Cholesterol</span>
        <div class="nutrient-amount">
          <input v-model="cholesterol" type="text" placeholder="30" />
          <span>mg</span>
        </div>
      </div>
      
      <div class="nutrient-row">
        <span class="nutrient-label sub-label">Sodium</span>
        <div class="nutrient-amount">
          <input v-model="sodium" type="text" placeholder="470" />
          <span>mg</span>
        </div>
      </div>
      
      <div class="nutrient-row">
        <span class="nutrient-label">Total Carbohydrate</span>
        <div class="nutrient-amount">
          <input v-model="totalCarbs" type="text" placeholder="31" @input="recalculateValues" />
          <span>g</span>
        </div>
      </div>
      
      <div class="nutrient-row sub-nutrient">
        <span class="nutrient-label sub-label">Dietary Fiber</span>
        <div class="nutrient-amount">
          <input v-model="dietaryFiber" type="text" placeholder="0" />
          <span>g</span>
        </div>
      </div>
      
      <div class="nutrient-row sub-nutrient">
        <span class="nutrient-label sub-label">Sugars</span>
        <div class="nutrient-amount">
          <input v-model="sugars" type="text" placeholder="5" />
          <span>g</span>
        </div>
      </div>
      
      <div class="nutrient-row">
        <span class="nutrient-label">Protein</span>
        <div class="nutrient-amount">
          <input v-model="protein" type="text" placeholder="5" @input="recalculateValues" />
          <span>g</span>
        </div>
      </div>
    </div>

    <div class="thick-line"></div>
    
    <div class="controls">
      <button v-if="!baseValuesSet" @click="setBaseValues" class="set-base-btn">
        Set Base Values
      </button>
      <button v-if="baseValuesSet" @click="resetValues" class="reset-btn">
        Reset
      </button>
    </div>
  </div>
</template>

<script setup>
import { ref } from 'vue'

// Reactive data for all nutrition inputs
const servingSize = ref('228')
const servingsPerContainer = ref('456')
const calories = ref('250')
const totalFat = ref('12')
const saturatedFat = ref('3')
const cholesterol = ref('30')
const sodium = ref('470')
const totalCarbs = ref('31')
const dietaryFiber = ref('0')
const sugars = ref('5')
const protein = ref('5')

// Base values and calculation state
const baseValuesSet = ref(false)
const baseValues = ref({
  servingSize: 0,
  servingsPerContainer: 0,
  calories: 0,
  totalFat: 0,
  totalCarbs: 0,
  protein: 0
})

// Set base values and lock readonly fields
function setBaseValues() {
  baseValues.value = {
    servingSize: parseFloat(servingSize.value) || 0,
    servingsPerContainer: parseFloat(servingsPerContainer.value) || 0,
    calories: parseFloat(calories.value) || 0,
    totalFat: parseFloat(totalFat.value) || 0,
    totalCarbs: parseFloat(totalCarbs.value) || 0,
    protein: parseFloat(protein.value) || 0
  }
  baseValuesSet.value = true
}

// Reset to original state
function resetValues() {
  baseValuesSet.value = false
  // Restore original placeholder values
  servingSize.value = '228'
  servingsPerContainer.value = '456'
  calories.value = '250'
  totalFat.value = '12'
  totalCarbs.value = '31'
  protein.value = '5'
}

// Recalculate serving size and calories based on nutrient changes
function recalculateValues() {
  if (!baseValuesSet.value) return
  
  const currentFat = parseFloat(totalFat.value) || 0
  const currentCarbs = parseFloat(totalCarbs.value) || 0
  const currentProtein = parseFloat(protein.value) || 0
  
  // Calculate differences from base values
  const fatDiff = currentFat - baseValues.value.totalFat
  const carbsDiff = currentCarbs - baseValues.value.totalCarbs
  const proteinDiff = currentProtein - baseValues.value.protein
  
  // Update serving size (base + mass differences)
  const newServingSize = baseValues.value.servingSize + fatDiff + carbsDiff + proteinDiff
  servingSize.value = newServingSize.toString()
  
  // Update calories using: base + (fat × 9) + (carbs × 4) + (protein × 4)
  const newCalories = baseValues.value.calories + (fatDiff * 9) + (carbsDiff * 4) + (proteinDiff * 4)
  calories.value = Math.round(newCalories).toString()
}
</script>

<style scoped>
.nutrition-facts {
  width: 280px;
  border: 2px solid black;
  font-family: Arial, sans-serif;
  background: white;
  padding: 8px;
  margin: 20px;
}

.title {
  font-size: 32px;
  font-weight: bold;
  text-align: left;
  margin-bottom: 4px;
}

.serving-info {
  font-size: 14px;
  margin-bottom: 8px;
}

.serving-size {
  display: flex;
  align-items: center;
  gap: 8px;
  margin-bottom: 2px;
}

.serving-size input {
  width: 40px;
  border: 1px solid #ccc;
  padding: 2px 4px;
  font-size: 14px;
}

.servings-per-container {
  display: flex;
  align-items: center;
  gap: 8px;
}

.servings-per-container input {
  width: 50px;
  border: 1px solid #ccc;
  padding: 2px 4px;
  font-size: 14px;
}

.thick-line {
  height: 8px;
  background-color: black;
  margin: 4px 0;
}

.medium-line {
  height: 4px;
  background-color: black;
  margin: 8px 0;
}

.calories-section {
  margin-bottom: 8px;
}

.amount-per-serving {
  font-size: 12px;
  font-weight: bold;
  margin-bottom: 4px;
}

.calories-row {
  display: flex;
  justify-content: space-between;
  align-items: center;
  font-size: 18px;
  font-weight: bold;
}

.calories-input {
  width: 60px;
  border: 1px solid #ccc;
  padding: 4px;
  font-size: 18px;
  font-weight: bold;
  text-align: right;
}

.nutrients-section {
  font-size: 14px;
}

.nutrient-row {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 2px 0;
  border-bottom: 1px solid #999;
}

.nutrient-row:last-child {
  border-bottom: none;
}

.sub-nutrient {
  padding-left: 16px;
  font-size: 13px;
}

.nutrient-label {
  font-weight: bold;
  flex: 1;
  text-align: left;
}
.nutrient-label.sub-label {
  font-weight: normal;
  font-size: 90%;
}

.nutrient-amount {
  display: flex;
  align-items: center;
  gap: 4px;
  font-weight: bold;
  width: 30%;
  justify-content: right;
}

.nutrient-amount input {
  width: 40px;
  border: 1px solid #ccc;
  padding: 2px 4px;
  font-size: 14px;
  text-align: right;
  font-weight: bold;
}

input:focus {
  outline: 2px solid #007bff;
  border-color: #007bff;
}

input::placeholder {
  color: #999;
  font-weight: normal;
}

input:read-only {
  background-color: #f5f5f5;
  color: #666;
  cursor: not-allowed;
}

.controls {
  margin-top: 16px;
  text-align: center;
}

.set-base-btn, .reset-btn {
  background-color: #007bff;
  color: white;
  border: none;
  padding: 8px 16px;
  font-size: 14px;
  border-radius: 4px;
  cursor: pointer;
  font-weight: bold;
}

.set-base-btn:hover, .reset-btn:hover {
  background-color: #0056b3;
}

.reset-btn {
  background-color: #6c757d;
}

.reset-btn:hover {
  background-color: #545b62;
}
</style> 