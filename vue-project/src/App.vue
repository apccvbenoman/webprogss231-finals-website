<script setup>
import { ref, onMounted } from 'vue';
import { supabase } from './lib/supabaseClient';

const guestbookEntries = ref([]);
const name = ref('');
const message = ref('');

const fetchGuestbook = async () => {
  const { data, error } = await supabase.from('guestbook').select('*').order('created_at', { ascending: false });
  if (error) console.error(error);
  else guestbookEntries.value = data;
};

const addEntry = async () => {
  if (!name.value || !message.value) return;
  const { error } = await supabase.from('guestbook').insert([{ name: name.value, message: message.value }]);
  if (error) console.error(error);
  else {
    name.value = '';
    message.value = '';
    fetchGuestbook(); // Refresh the list
  }
};

onMounted(fetchGuestbook);
</script>

<template>
  <div class="container">
    <img :src="'/profile.jpg'" alt="Profile" class="profile-img" />
    <h1>Clarice V. Benoman</h1>
    <p>Hey, I'm Clar! I'm 19 years old, a 2nd year BSCS-SS Student here at APC, and down below are other things about me!</p>
    
    <h2>Education & Achievements</h2>
    <ul>
      <li>Graduated Elementary from Hope in Christ Christian Academy</li>
      <li>Graduated High School from Philippine Pasay Chung Hua Academy</li>
      <li>Studied a bit of Mandarin in High School</li>

    </ul>

    <h2>IT Experience</h2>
    <ul>
      <li>Database Management</li>
      <li>School Programming Tasks</li>
    </ul>

    <h2>Hobbies & Interests</h2>
    <ul>
      <li>Watching movies/series</li>
      <li>Learning something new to cook</li>
    </ul>

    <h2>Goals</h2>
    <ul>
      <li>To try to race a car, responsibly!</li>
      <li>My ultimate goal is to provide a comfortable life for myself and people that I love and live it with contentment</li>
    </ul>

    <h2>Photo Gallery</h2>
    <div class="gallery">
      <img v-for="i in 6" :key="i" :src="`/profile${i+1}.jpg`" alt="Gallery Image" class="gallery-img" />
    </div>

    <h2>Guestbook</h2>
    <form @submit.prevent="addEntry">
      <input v-model="name" placeholder="Your Name" required />
      <textarea v-model="message" placeholder="Your Message" required></textarea>
      <button type="submit">Sign Guestbook</button>
    </form>

    <ul>
      <li v-for="entry in guestbookEntries" :key="entry.id">
        <strong>{{ entry.name }}</strong>: {{ entry.message }}
      </li>
    </ul>
  </div>
</template>

<style scoped>
.container { max-width: 600px; margin: auto; padding: 20px; text-align: center; }
.profile-img { width: 150px; height: 150px; border-radius: 50%; object-fit: cover; }
input, textarea { width: 100%; margin: 5px 0; padding: 8px; }
button { padding: 8px 15px; cursor: pointer; }
ul { list-style: none; padding: 0; }
li { padding: 10px; border-bottom: 1px solid #ccc; }
.gallery { display: grid; grid-template-columns: repeat(3, 1fr); gap: 10px; margin-top: 10px; }
.gallery-img { width: 100%; height: auto; border-radius: 10px; }
</style>