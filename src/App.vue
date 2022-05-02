<!--
TODO 
add UI with v-for
v-for: should be looping through every category name field
add logic for weights
maybe let people delete and add categories
remove field is not complete 
could use vue documentation
class vue activities 
composition api


in input grade buttons maybe use this? idk why it doesnt work
v-for="gradeName in state.categories[i]"
          :key="gradeName.category"


eq: grades * weight for every category

figure out algorithms

-->

<script setup>
import { ref, reactive } from "vue";

const state = reactive({ categories: [] });

var categoryName = ref("");
var categoryWeight = ref();
var gradeName = ref("");
var grade = ref(0);

function createCategory() {
  var sum = 0;
  if (categoryName.value !== "" && categoryWeight.value > 0 && categoryWeight.value <= 1) {
    for (var i = 0; i < state.categories.length; i++) {
      sum += parseFloat(state.categories[i].categoryWeight);
    }
    if (sum < 1) {
      console.log(sum);
      var temp = {
        "categoryName": categoryName.value,
        "categoryWeight": categoryWeight.value,
        "fields": [],
      }
      state.categories.push(temp);
    } else if(sum>1){
      alert("Your grade weights will exceed 100%");
    }
  } else {
    alert("You should input a number between 0 and 1 for the grade weight (i.e .15) and a valid category name");
    console.log("there is an error here");
  }

}
function addField(cName) {
  if (gradeName.value !== "") {
    var temp2 = {
      "gradeName": gradeName.value,
      "grade": grade.value,
    }
    //const found = categories.find(element => element.categoryName==cName);
    for (var i = 0; i < state.categories.length; i++) {

      if (state.categories[i].categoryName == cName) {
        state.categories[i].fields.push(temp2);

        console.log(state.categories[i].categoryName);
      }

    }
  } else {
    alert("Please enter a valid grade name and number");
  }

}

function calculate() {
  var estCompletion = 0;
  var wantedAvg = 0;
  var sum = 0;
  var fieldSum = 0;
  var categoryAvg = 0;
  var currentAvg = 0;
  var currentGrade = 0;
  var neededCompletion = 0;
  var neededGrade = 0;
  var count = 0;
  estCompletion = prompt("Enter your estimated completion", .5);
  wantedAvg = prompt("Enter your wanted grade", .9);
  for (var i = 0; i < state.categories.length; i++) {
    sum += parseFloat(state.categories[i].categoryWeight);
  }
  console.log(sum);
  if (sum == 1) {
    for (var i = 0; i < state.categories.length; i++) {
      for (var j = 0; j < state.categories[i].fields.length; j++) {
        fieldSum += parseFloat(state.categories[i].fields[j].grade);
        console.log("Realsum:" + fieldSum);
        count++;
      }
      fieldSum = fieldSum / count;
      console.log("categoryWeight: " + state.categories[i].categoryWeight)
      categoryAvg = fieldSum * parseFloat(state.categories[i].categoryWeight);
      console.log("categoryAvg: " + categoryAvg);
      currentAvg += parseFloat(categoryAvg);

      count = 0;
      fieldSum = 0;
    }
    console.log("estCompletion:" + estCompletion);
    console.log("RN avg " + currentAvg)
    currentGrade = parseFloat(currentAvg) * parseFloat(estCompletion);
    console.log("currentGrade:" + currentGrade);
    neededCompletion = 1 - parseFloat(estCompletion);
    neededGrade = (parseFloat(wantedAvg) - currentGrade) / neededCompletion;
    console.log("neededGrade:" + neededGrade);
    document.getElementById("gradeNumbers").innerHTML = "Average Grade: " + currentAvg.toFixed(2);
    document.getElementById("gradeNumbers2").innerHTML = "Current Average Grade Based on class completion: " + currentGrade.toFixed(2);
    document.getElementById("gradeNumbers3").innerHTML = "Average Grade Needed to reach desired grade: " + neededGrade.toFixed(2);
    var temp3 = {
      "RNavg": currentAvg,
      "currentAvg": currentGrade,
      "neededGrade": neededGrade,
    }
    //   state.categories.push(temp3);
  } else {
    console.log("hi");
    alert("Your grade weights do not reach 100%")
  }

}


</script>

<template>
  <header>

    <h1>Grade Prediction Calculator</h1>

  </header>
  <main>

    <p>
      This is a tool to help you get a rough estimate of a general grade you need to get (for the remainder of a class) 
      to get a certain grade in your class. To start off you
      can begin by entering the different assignment types
      and how much they are worth. For each grade category enter the assignment category in the first input field(i.e quizzes, homework, etc). 
      Then enter an associated weight with that category in the second input field (i.e .45). Finally, you can click the "New Category" button to 
      add that new category.
    </p>
    <button @click="calculate" id="calculateButton">Calculate</button>
    <input v-model="categoryName" type="text" placeholder="Plug in Category Name" />
    <input v-model="categoryWeight" type="text" placeholder="Plug in Category weight" />
    <button @click="createCategory" id="theButton">New Category</button>


    <div style="margin-left:20px; margin-right: 20px">
      <div class="form-group" v-for="category in state.categories" :key="category.categoryName">

        <label style="font-weight: bold; font-size:large;">{{ category.categoryName }}
          ({{ category.categoryWeight }})</label>
        <div class="inputs grade buttons">

          <input v-model="gradeName" type="text" class=""
            placeholder="Enter grades for each assignment type seperated by commas ex: 2/2,4/6,8/10" />

          <input v-model="grade" type="text" class=""
            placeholder="Enter the grade categories weight ex: Homework, worth 15% of final (only list the percent)" />

          <button @click="addField(category.categoryName)" id="addButton">Add</button>

          <div v-for="field in category.fields" :key="field.id" style="display:block">

            <div>
              {{ field.gradeName }}
            </div>
            <input v-model="field.grade" type="text" class="" placeholder="Enter the grade)" />

          </div>

        </div>


      </div>
      <p id="gradeNumbers">
        Current Average Grade:
      </p>
      <p id="gradeNumbers2">
        Current Average Grade Based on class completion:
      </p>
      <p id="gradeNumbers3">
        Average Grade Needed to reach desired grade:
      </p>

    </div>
  </main>
</template>

<style>
@import './assets/base.css';

#addButton {
  border: 2px solid #e7e7e7;
  background-color: rgb(43, 168, 43);
  border-radius: 50px;
  width: 100%;
  padding: 10px 24px;
  color: floralwhite;
  margin: 20 auto;
  display: block;

}

#calculateButton {
  border: 2px solid #e7e7e7;
  background-color: cornflowerblue;
  margin: 0 auto;
  display: block;
  border-radius: 50px;
  width: 250px;
  padding: 10px 24px;
  color: floralwhite;
}

#removeButton {
  border: 2px solid #e7e7e7;
  background-color: tomato;
  border-radius: 50px;
  width: 50%;
  padding: 10px 24px;
  color: floralwhite;
  margin: 0 auto;
  display: block;

}

#theButton {
  border: 2px solid #e7e7e7;
  background-color: cornflowerblue;
  margin: 0 auto;
  display: block;
  border-radius: 50px;
  width: 250px;
  padding: 10px 24px;
  color: floralwhite;

}

body {

  color: #c0c999;
  background-color: #3b1c32;
  margin-left: 75px;
  margin-right: 75px;

}

p {
  text-align: center;
  margin-left: 300px;
  margin-right: 300px;
}

header,
footer {

  text-align: center;

  font-weight: bold;
}

h1,
h2,
h3 {


  text-align: center;
}

h3 {
  font-size: 200%;
  line-height: 0px;
}



figure {
  text-align: center;
}
</style>
