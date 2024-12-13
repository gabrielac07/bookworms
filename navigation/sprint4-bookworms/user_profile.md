---
layout: post
title: Profile Settings
permalink: /bookworms_profile/
menu: nav/home.html
search_exclude: true
show_reading_time: false
---

<div class="profile-container">
  <div class="card">
    <form>
      <div>
        <label for="newUid">Enter New UID:</label>
        <input type="text" id="newUid" placeholder="New UID">
      </div>
      <div>
        <label for="newName">Enter New Name:</label>
        <input type="text" id="newName" placeholder="New Name">
      </div>
      <div>
        <label for="newPassword">Enter New Password:</label>
        <input type="text" id="newPassword" placeholder="New Password">
      </div>
      <br>
      <div>
        <label for="kasmServerNeeded">Kasm Server Needed:
          <input type="checkbox" id="kasmServerNeeded" onclick="toggleKasmServerNeeded()">
        </label>
      </div>
      <br>
      <div>
        <label for="sectionDropdown">Select and Add Section:</label>
        <div class="icon-container">
          <select id="sectionDropdown">
            <!-- Options will be dynamically populated -->
          </select>
          <i class="fas fa-plus" onclick="addSection()"></i>
        </div>
      </div>
      <table>
        <thead>
          <tr>
            <th>Abbreviation</th>
            <th>Name</th>
            <th>Year</th>
          </tr>
        </thead>
        <tbody id="profileResult">
          <!-- Table rows will be dynamically populated -->
        </tbody>
      </table>
      <label for="profilePicture" class="file-icon"> Upload Profile Picture <i class="fas fa-upload"></i></label>
      <input type="file" id="profilePicture" accept="image/*" onchange="saveProfilePicture()">
      <div class="image-container" id="profileImageBox">
        <!-- Profile picture will be displayed here -->
      </div>
      <p id="profile-message" style="color: red;"></p>
    </form>
  </div>
</div>


<script type="module">
  import { pythonURI, fetchOptions } from '{{site.baseurl}}/assets/js/api/config.js';
  import { putUpdate, postUpdate, deleteData, logoutUser } from "{{site.baseurl}}/assets/js/api/profile.js";

  let predefinedSections = [];
  let userSections = [];

  // Function to fetch predefined sections from kasm2_backend
  async function fetchPredefinedSections() {
    const URL = pythonURI + "/api/section";
    try {
      const response = await fetch(URL, fetchOptions);
      if (!response.ok) {
        throw new Error(`Failed to fetch predefined sections: ${response.status}`);
      }
      return await response.json();
    } catch (error) {
      console.error('Error fetching predefined sections:', error.message);
      return [];
    }
  }

  // Function to populate the section dropdown menu
  function populateSectionDropdown(predefinedSections) {
    const sectionDropdown = document.getElementById('sectionDropdown');
    sectionDropdown.innerHTML = '';
    predefinedSections.forEach(section => {
      const option = document.createElement('option');
      option.value = section.abbreviation;
      option.textContent = `${section.abbreviation} - ${section.name}`;
      sectionDropdown.appendChild(option);
    });
    displayProfileSections();
  }

  // Function to add a section
  window.addSection = async function () {
    const dropdown = document.getElementById('sectionDropdown');
    const selectedOption = dropdown.options[dropdown.selectedIndex];
    const abbreviation = selectedOption.value;
    const name = selectedOption.textContent.split(' ').slice(1).join(' ');

    if (!abbreviation || !name) {
      document.getElementById('profile-message').textContent = 'Please select a section from the dropdown.';
      return;
    }

    document.getElementById('profile-message').textContent = '';

    const sectionExists = userSections.some(section => section.abbreviation === abbreviation && section.name === name);
    if (!sectionExists) {
      userSections.push({ abbreviation, name });
      displayProfileSections();
      await saveSections();
    }
  }

  // Function to display the sections in the table
  function displayProfileSections() {
    const tableBody = document.getElementById('profileResult');
    tableBody.innerHTML = '';
    userSections.forEach(section => {
      const tr = document.createElement('tr');
      const abbreviationCell = document.createElement('td');
      const nameCell = document.createElement('td');
      const yearCell = document.createElement('td');
      abbreviationCell.textContent = section.abbreviation;
      nameCell.textContent = section.name;
      yearCell.textContent = section.year;

      tr.appendChild(abbreviationCell);
      tr.appendChild(nameCell);
      tr.appendChild(yearCell);
      tableBody.appendChild(tr);
    });
  }

  // Function to save sections
  async function saveSections() {
    const sectionAbbreviations = userSections.map(section => section.abbreviation);
    const sectionsData = { sections: sectionAbbreviations };
    const URL = pythonURI + "/api/user/section";
    const options = { URL, body: sectionsData, message: 'profile-message', callback: async () => { console.log('Sections saved successfully!'); await fetchDataAndPopulateTable(); } };

    try {
      await postUpdate(options);
    } catch (error) {
      console.error('Error saving sections:', error.message);
      document.getElementById('profile-message').textContent = 'Error saving sections: ' + error.message;
    }
  }

  // Function to fetch and display the user profile data
  async function fetchUserProfile() {
    const URL = pythonURI + "/api/id/pfp";
    try {
      const response = await fetch(URL, fetchOptions);
      if (!response.ok) {
        throw new Error(`Failed to fetch user profile: ${response.status}`);
      }
      const profileData = await response.json();
      displayUserProfile(profileData);
    } catch (error) {
      console.error('Error fetching user profile:', error.message);
    }
  }

  // Function to display user profile data
  function displayUserProfile(profileData) {
    const profileImageBox = document.getElementById('profileImageBox');
    if (profileData.pfp) {
      const img = document.createElement('img');
      img.src = `data:image/jpeg;base64,${profileData.pfp}`;
      img.alt = 'Profile Picture';
      profileImageBox.innerHTML = '';
      profileImageBox.appendChild(img);
    } else {
      profileImageBox.innerHTML = '<p>No profile picture available.</p>';
    }
  }

  // Function to save profile picture
  window.saveProfilePicture = async function () {
    const fileInput = document.getElementById('profilePicture');
    const file = fileInput.files[0];
    if (file) {
      const reader = new FileReader();
      reader.onload = function() {
        const profileImageBox = document.getElementById('profileImageBox');
        profileImageBox.innerHTML = `<img src="${reader.result}" alt="Profile Picture">`;
      };
      reader.readAsDataURL(file);
    }

    if (!file) return;

    try {
      const base64String = await convertToBase64(file);
      await sendProfilePicture(base64String);
      console.log('Profile picture uploaded successfully!');
    } catch (error) {
      console.error('Error uploading profile picture:', error.message);
    }
  }

  // Function to convert file to base64
  async function convertToBase64(file) {
    return new Promise((resolve, reject) => {
      const reader = new FileReader();
      reader.onload = () => resolve(reader.result.split(',')[1]);
      reader.onerror = error => reject(error);
      reader.readAsDataURL(file);
    });
  }

  // Function to send profile picture to server
  async function sendProfilePicture(base64String) {
    const URL = pythonURI + "/api/id/pfp";
    const options = {
      URL,
      body: { pfp: base64String },
      message: 'profile-message',
      callback: () => { console.log('Profile picture uploaded successfully!'); }
    };

    try {
      await putUpdate(options);
    } catch (error) {
      console.error('Error uploading profile picture:', error.message);
      document.getElementById('profile-message').textContent = 'Error uploading profile picture: ' + error.message;
    }
  }

  // Function to fetch and set UID and Name placeholders
  window.setPlaceholders = async function() {
    const uidInput = document.getElementById('newUid');
    const nameInput = document.getElementById('newName');

    try {
      const uid = await window.fetchUid();
      const name = await window.fetchName();

      if (uid !== null) {
        uidInput.placeholder = uid;
      }
      if (name !== null) {
        nameInput.placeholder = name;
      }
    } catch (error) {
      console.error('Error setting placeholders:', error.message);
    }
  };

  // Event listeners to trigger functions when UID, Name, or Password are changed
  document.getElementById('newUid').addEventListener('change', function() {
    const uid = this.value;
    window.changeUid(uid);
  });

  document.getElementById('newName').addEventListener('change', function() {
    const name = this.value;
    window.changeName(name);
  });

  document.getElementById('newPassword').addEventListener('change', function() {
    const password = this.value;
    window.changePassword(password);
  });

  document.addEventListener('DOMContentLoaded', async function () {
    try {
      predefinedSections = await fetchPredefinedSections();
      populateSectionDropdown(predefinedSections);
      await fetchUserProfile();
      await fetchDataAndPopulateTable();
      await fetchKasmServerNeeded();
      await setPlaceholders();
    } catch (error) {
      console.error('Initialization error:', error.message);
    }
  });
</script>







