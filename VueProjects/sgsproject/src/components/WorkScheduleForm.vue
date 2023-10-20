<template>
  <div>
    <h1>Выберите сотрудника</h1>
    <form id="workScheduleForm">
      <label for="citySelect">Город: </label>
      <select ref="citySelect" v-on:change="handleCityChange"></select><br>

      <label for="workshopSelect">Цех: </label>
      <select ref="workshopSelect" v-on:change="handleWorkshopChange"></select><br>

      <label for="teamSelect">Бригада: </label>
      <select ref="teamSelect" v-on:change="handleTeamChange"></select><br>

      <label for="employeeSelect">Сотрудник: </label>
      <select ref="employeeSelect"></select><br>

      <button class="btn btn-secondary" type="submit">Do something button</button>
    </form>
  </div>
</template>

<script>
import Cookies from 'js-cookie';

export default {
  data() {
    return {
      employees: [
        { name: "Иванов Иван" },
        { name: "Петров Петр" },
        { name: "Сидоров Андрей" },
        { name: "Холобаев Виктор" },
        { name: "Нечаев Артем" },
        { name: "Новиков Илья" },
        { name: "Донских Павел" },
        { name: "Горных Денис" }
      ],
      teams: [
        { name: "Бригада 1", employees: [0] },
        { name: "Бригада 2", employees: [1, 2] },
        { name: "Бригада 1", employees: [3, 4] },
        { name: "Бригада 2", employees: [5] },
        { name: "Бригада 1", employees: [6] },
        { name: "Бригада 2", employees: [7] }
      ],
      workshops: [
        { name: "Цех 1", teams: [0, 1] },
        { name: "Цех 2", teams: [2, 3] },
        { name: "Цех 3", teams: [4, 5] }
      ],
      cities: [
        { name: "Тула", workshops: [0, 1] },
        { name: "Кострома", workshops: [2] }
      ],
    };
  },
  methods: {
    addOptions(selectElement, options) {
      selectElement.length = 0; // Clear the select options
      options.forEach(option => {
        selectElement.add(new Option(option.name, option.name));
      });
    },
    handleCityChange() {
      this.selectedCity = this.cities.find(city => city.name === this.citySelect.value);
      if (this.selectedCity) {
        this.addOptions(this.workshopSelect, this.workshops.filter(workshop => this.selectedCity.workshops.includes(this.workshops.indexOf(workshop))));
        this.handleWorkshopChange();
        Cookies.set('selectedCity', this.citySelect.value);
      }
    },
    handleWorkshopChange() {
      this.selectedCity = this.cities.find(city => city.name === this.citySelect.value);
      this.selectedWorkshop = this.workshops.filter(workshop => this.selectedCity.workshops.includes(this.workshops.indexOf(workshop))).find(workshop => workshop.name === this.workshopSelect.value);
      if (this.selectedWorkshop) {
        this.addOptions(this.teamSelect, this.teams.filter(team => this.selectedWorkshop.teams.includes(this.teams.indexOf(team))));
        this.handleTeamChange();
        Cookies.set('selectedWorkshop', this.workshopSelect.value);
      }
    },
    handleTeamChange() {
      this.selectedCity = this.cities.find(city => city.name === this.citySelect.value);
      this.selectedWorkshop = this.workshops.filter(workshop => this.selectedCity.workshops.includes(this.workshops.indexOf(workshop))).find(workshop => workshop.name === this.workshopSelect.value);
      this.selectedTeam = this.teams.filter(team => this.selectedWorkshop.teams.includes(this.teams.indexOf(team))).find(team => team.name === this.teamSelect.value);
      if (this.selectedTeam) {
        this.addOptions(this.employeeSelect, this.employees.filter(employee => this.selectedTeam.employees.includes(this.employees.indexOf(employee))));
      }
      Cookies.set('selectedTeam', this.teamSelect.value);
      
      Cookies.set('selectedEmployee', this.employeeSelect.value);
    },
  },
  mounted() {
    // Get select elements using Vue refs
    this.citySelect = this.$refs.citySelect;
    this.workshopSelect = this.$refs.workshopSelect;
    this.teamSelect = this.$refs.teamSelect;
    this.employeeSelect = this.$refs.employeeSelect;

    // Initial update when the page loads
    const firstCityWorkshops = this.workshops.filter(workshop =>
      this.cities[0].workshops.includes(this.workshops.indexOf(workshop))
    );

    const firstWorkshopTeams = this.teams.filter(team =>
        firstCityWorkshops[0].teams.includes(this.teams.indexOf(team))
    );

    const firstTeamEmployees = firstWorkshopTeams[0].employees.map(index => this.employees[index]);

    this.addOptions(this.citySelect, this.cities);
    this.addOptions(this.workshopSelect, firstCityWorkshops);
    this.addOptions(this.teamSelect, firstWorkshopTeams);
    this.addOptions(this.employeeSelect, firstTeamEmployees);

    const selectedCity = Cookies.get('selectedCity');
    const selectedWorkshop = Cookies.get('selectedWorkshop');
    const selectedTeam = Cookies.get('selectedTeam');
    const selectedEmployee = Cookies.get('selectedEmployee');
    
    this.citySelect.value = this.cities.find(city => city.name == selectedCity).name;

    const selectedWorkshopsOptions = this.workshops.filter(workshop => this.cities.find(city => city.name == selectedCity).workshops.includes(this.workshops.indexOf(workshop)));    
    this.addOptions(this.workshopSelect, selectedWorkshopsOptions);
    this.workshopSelect.value = this.workshops.find(workshop => workshop.name == selectedWorkshop).name;
    
    const selectedTeamsOptions = this.teams.filter(team => selectedWorkshopsOptions.find(workshop => workshop.name == selectedWorkshop).teams.includes(this.teams.indexOf(team)));    
    this.addOptions(this.teamSelect, selectedTeamsOptions);
    this.teamSelect.value = this.teams.find(team => team.name == selectedTeam).name;

    const selectedEmployeeOptions = this.employees.filter(employee => selectedTeamsOptions.find(team => team.name == selectedTeam).employees.includes(this.employees.indexOf(employee)));
    this.addOptions(this.employeeSelect, selectedEmployeeOptions);
    this.employeeSelect.value = this.employees.find(employee => employee.name == selectedEmployee).name;

    // Add event listeners for change events
    this.citySelect.addEventListener("change", this.handleCityChange);
    this.workshopSelect.addEventListener("change", this.handleWorkshopChange);
    this.teamSelect.addEventListener("change", this.handleTeamChange);
  }
};
</script>