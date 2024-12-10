<script setup>
import { ref } from 'vue';
import { useForm } from '@inertiajs/vue3';

// Membuat form untuk Chirp
const form = useForm({
    message: '',
    image: null,
});

const file = ref(null);
const imageUrl = ref(null);

// Fungsi untuk menangani upload file
const handleFileUpload = (event) => {
    file.value = event.target.files[0];
    if (file.value) {
        const reader = new FileReader();
        reader.onload = (e) => {
            imageUrl.value = e.target.result; // Menampilkan pratinjau gambar
        };
        reader.readAsDataURL(file.value);
    }
};

// Fungsi untuk mengirim form
const submitChirp = () => {
    const formData = new FormData();
    formData.append('message', form.message);
    if (file.value) {
        formData.append('image', file.value);
    }

    form.post(route('chirps.store'), {
        data: formData,
        onSuccess: () => {
            file.value = null;
            imageUrl.value = null;
            form.reset();
        },
    });
};
</script>

<template>
<div class="p-4 border rounded shadow">
    <form @submit.prevent="submitChirp">
        <textarea v-model="form.message" placeholder="Type your chirp..." class="border w-full p-2 rounded"></textarea>
        <input type="file" @change="handleFileUpload" class="mt-2" />
        <img v-if="imageUrl" :src="imageUrl" alt="Preview" class="mt-2 w-full rounded-md" />
        <div class="mt-2">
            <button type="submit" class="bg-blue-500 text-white px-4 py-2 rounded">Submit</button>
        </div>
    </form>
</div>
</template>