<template>
  <div :key="render">
    <UCard>
      <div class="flex justify-evenly  w-11/12">
        <UButton class="bg-blue-200" @click="openModal('add')">Add Details</UButton>
      </div>
   
      <UModal v-model="showModal">
        
        <UCard>
          <div class="text-right">
          <UButton color="black" @click="closeModal"><svg xmlns="http://www.w3.org/2000/svg" width="1em" height="1em" viewBox="0 0 32 32"><path fill="currentColor" d="M16 2C8.2 2 2 8.2 2 16s6.2 14 14 14s14-6.2 14-14S23.8 2 16 2m5.4 21L16 17.6L10.6 23L9 21.4l5.4-5.4L9 10.6L10.6 9l5.4 5.4L21.4 9l1.6 1.6l-5.4 5.4l5.4 5.4z"/></svg>
            </UButton></div>
          <form v-if="operationType !== 'delete'">
            <p>
              Name: <input type="text" v-model="formData.name" />
            </p>
            <p>
              Age: <input type="number" v-model="formData.age" />
            </p>
            <p class="flex">
              Gender:
              <select v-model="formData.gender" class="h-5">
                <option value="Male" >Male</option>
                <option value="Female">Female</option>
              </select>
              <!-- Display gender icon in Add/Edit block -->
              <span v-if="operationType === 'add' || operationType === 'edit'" style="margin-left: 200px; margin-top:0px; margin-bottom:20px; font-size: 6.5em;" v-html="getGenderIcon(formData.gender)"></span>
            </p>
          </form>
<template #footer>
          <div class="justify-center" v-if="operationType === 'delete'">
            <p>Do you want to Delete ? </p>
            <div class="flex justify-center">
              <UButton color="black" @click="performDelete">Yes</UButton>
              <UButton color="black" @click="cancelDelete">No</UButton>
            </div>
          </div>

          <div class="flex justify-center" v-else>
            <UButton color="black" @click="performOperation">
              {{ operationType === 'edit' ? 'Update' : 'Save' }}
            </UButton>
            <UButton color="black" @click="closeModal">close
            </UButton>
          </div></template>
        </UCard>
      </UModal>
    <div class="rounded-lg">
      <table >
        <thead>
          <tr>
            <th class="bg-blue-200">Name</th>
            <th class="bg-blue-200">Age</th>
            <th class="bg-blue-200">Gender</th>
            <th class="bg-blue-200">Actions</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="(item, idx) of details" :key="idx">
            <td class="bg-pink-50">{{ item.name }}</td>
            <td class="bg-pink-50">{{ item.age }}</td>
            <td class="bg-pink-50">
              {{item.gender}}
            </td>
            <td class="bg-pink-50">
              <UButton class="bg-blue-200" @click="openModal('edit', idx)">Edit</UButton>
              <UButton class="bg-blue-200" @click="openModal('delete', idx)">Delete</UButton>
            </td>
          </tr>
        </tbody>
       
      </table></div>
    </UCard>
 
</div>
</template>

<script setup>

const details = ref();
onMounted(()=>{details.value=JSON.parse(localStorage.getItem('mydata')) || []})

const showModal = ref(false);
const confirmDeleteModal = ref(false);
const formData = ref({ name: '', age: 0, gender: '' });

const render=ref(0)
let operationType = null;
let operationIndex = null;

const openModal = (type, index = null) => {
  operationType = type;
  operationIndex = index;

  if (type === 'add') {
    formData.value = { name: '', age: 0, gender: '' };
  } else if (type === 'edit' && index !== null) {
    formData.value = { ...details.value[index] };
  }

  showModal.value = true;
};

const performOperation = () => {
  
  if (operationType === 'add') {
    details.value.push({ ...formData.value });
  } else if (operationType === 'edit' && operationIndex !== null) {
    details.value[operationIndex] = { ...formData.value };
  }
 localStorage.setItem('mydata',JSON.stringify(details.value))
  closeModal();
};

const confirmDelete = () => {
  confirmDeleteModal.value = true;
};

const performDelete = () => {
  details.value.splice(operationIndex, 1);
  closeModal();
  confirmDeleteModal.value = false;
  render.value++
  localStorage.setItem('mydata',JSON.stringify(details.value))
};

const cancelDelete = () => {
  confirmDeleteModal.value = false;
};

const closeModal = () => {
  showModal.value = false;
  operationType = null;
  operationIndex = null;
};

const getGenderIcon = (gender) => {
  return gender === 'Male'
    ? '<svg xmlns="http://www.w3.org/2000/svg" width="0.92em" height="1em" viewBox="0 0 22 24"><path fill="currentColor" d="M14.145 16.629a23.876 23.876 0 0 1-.052-2.525l-.001.037a4.847 4.847 0 0 0 1.333-2.868l.002-.021c.339-.028.874-.358 1.03-1.666a1.217 1.217 0 0 0-.455-1.218l-.003-.002c.552-1.66 1.698-6.796-2.121-7.326C13.485.35 12.479 0 11.171 0c-5.233.096-5.864 3.951-4.72 8.366a1.222 1.222 0 0 0-.455 1.229l-.001-.008c.16 1.306.691 1.638 1.03 1.666a4.858 4.858 0 0 0 1.374 2.888a24.648 24.648 0 0 1-.058 2.569l.005-.081C7.308 19.413 0.32 18.631 0 24h22.458c-.322-5.369-7.278-4.587-8.314-7.371z"/></svg>'
    : '<svg xmlns="http://www.w3.org/2000/svg" width="0.92em" height="1em" viewBox="0 0 22 24"><path fill="currentColor" d="M14.041 16.683a14.884 14.884 0 0 1-.035-.72c2.549-.261 4.338-.872 4.338-1.585c-.007 0-.006-.03-.006-.041C16.432 12.619 19.99.417 13.367.663a3.344 3.344 0 0 0-2.196-.664h.008C2.208.677 6.175 12.202 4.13 14.377h-.004c.008.698 1.736 1.298 4.211 1.566c-.007.17-.022.381-.054.734C7.256 19.447.321 18.671.001 24h22.294c-.319-5.33-7.225-4.554-8.253-7.317z"/></svg>';
};
</script>

<style>
  table {
    
    border-collapse: collapse;
    border-radius:1px;
    margin-top: 100px;
    display: contents;
    height: 0px !important;
    /* margin-left:150px;
    margin-right:10px; */
    width:100%;
   

    
  }
tbody{
  
}
  th, td {width: 413px;
    
    border: 2px solid  gray;
    border-radius: 5px !important;
    padding: 10px;
    text-align: center;
    
  }

  th {
    background-color: #f2f2f2;
  }
</style>
