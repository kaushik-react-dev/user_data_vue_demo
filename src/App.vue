<template>
  <div>
    <table>
      <thead>
        <tr>
          <th>SL No.</th>
          <th @click="sortData('name')">Name</th>
          <th>Dob</th>
          <th>Email</th>
          <th>Location</th>
          <th>Phone</th>
          <th>Picture</th>

        </tr>
      </thead>
      <tbody>
        <tr v-for="(user, index) in users" :key="user.slno">
          <td>{{ index + 1}}</td>
          <td>{{ user.name.title + " "+ user.name.first + " "+ user.name.last }}</td>          
          <td>{{ user.dob.date }}</td>
          <td>{{ user.email }}</td>
          <td>{{ user.location.street.number + " ,"+ user.location.street.name + ","+ user.location.city+ ","+ user.location.state + ","+ user.location.country + ","+ user.location.postcode}}</td>          
          <td>{{ user.phone }}</td>
          <td><img :src="user.picture.medium" alt="User Picture"></td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue';
import axios from 'axios';

const users = ref([]);

  console.log('users == ',users)

const columns = ref([
  { label: 'Sl No.', field: 'slno', sortable: true },
  { label: 'Name', field: 'name', sortable: true },
  { label: 'DOB', field: 'dob', sortable: true },
  { label: 'Email', field: 'email', sortable: true },
  { label: 'Location', field: 'location', sortable: true },
  { label: 'Phone', field: 'phone', sortable: true },
  { label: 'Picture', field: 'picture', sortable: false }
]);

const flattenedUsers = ref([]);

const fetchData = async () => {
  try {
    const response = await axios.get('https://randomuser.me/api/?results=50');
    users.value = response.data.results;
  } catch (error) {
    console.error('Error fetching user data:', error);
  }
};

const flattenUsers = () => {
  flattenedUsers.value = users.value.map((user, index) => ({
    slno: `${index + 1}`,
    name: `${user.name.first} ${user.name.last}`,
    dob: new Date(user.dob.date).toLocaleDateString(),
    email: user.email,
    location: `${user.location.city}, ${user.location.country}`,
    phone: user.phone,
    picture: user.picture.medium
  }));
};

// const sortData = (field) => {
//   flattenedUsers.value.sort((a, b) => {
//     return a[field] > b[field] ? 1 : -1;
//   });
// };

  const sortData = (field) => {
    flattenedUsers.value.sort((a, b) => {
      const aValue = a[field];
      const bValue = b[field];

      if (typeof aValue === 'string') {
        return aValue.localeCompare(bValue);
      } else {
        return aValue > bValue ? 1 : -1;
      }
    });
  };

onMounted(() => {
  fetchData();
});

flattenUsers();

</script>

<style scoped>
/* Add your custom styles here */
  table {
    width: 100%;
    border-collapse: collapse;
    margin-top: 20px;
  }

  th, td {
    border: 1px solid #dddddd;
    text-align: left;
    padding: 8px;
  }

  th {
    background-color: #f2f2f2;
    cursor: pointer;
  }

  tbody tr:nth-child(even) {
    background-color: #f9f9f9;
  }

  img {
    max-width: 50px;
    max-height: 50px;
  }
</style>