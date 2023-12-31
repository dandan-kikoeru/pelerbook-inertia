<template>
  <Head title="Settings | Pelerbook"></Head>
  <div
    class="card bg-neutral shadow-xl max-w-lg absolute left-1/2 top-1/2 -translate-x-1/2 -translate-y-1/2"
  >
    <div class="card-body">
      <form @submit.prevent="submitName">
        <div class="flex mx-auto gap-2">
          <input
            v-model="nameForm.firstname"
            type="text"
            class="input"
            :placeholder="auth.user.firstname"
            @input="handleName"
          />
          <input
            v-model="nameForm.surname"
            type="text"
            class="input"
            :placeholder="auth.user.surname"
            @input="handleName"
          />
        </div>
        <div class="flex">
          <button class="btn btn-primary mx-auto mt-4" v-if="showNameSubmit">
            Save
          </button>
        </div>
      </form>
      <div>
        <div class="flex justify-between items-center">
          Profile Picture
          <div
            class="btn btn-ghost text-primary normal-case"
            @click="openAvatarInput"
          >
            Edit
          </div>
        </div>
        <form @submit.prevent="submitAvatar()">
          <div class="flex flex-col mt-2">
            <input
              type="file"
              accept=".jpg, .jpeg, .png, .webp"
              @change="handleAvatarPreview"
              @input="avatarForm.avatar = $event.target.files[0]"
              ref="avatarInputRef"
              class="hidden"
            />
            <img
              :src="avatarPreview"
              class="h-32 w-32 rounded-full mx-auto"
              v-if="avatarPreview"
            />
            <button
              class="btn btn-primary mx-auto my-4 normal-case"
              v-if="!avatarError && avatarForm.avatar !== auth.user.avatar"
              :disabled="avatarForm.processing"
            >
              Save
            </button>
          </div>
        </form>
      </div>
      <div>
        <div class="flex justify-between items-center">
          Cover
          <div
            class="btn btn-ghost text-primary normal-case"
            @click="openCoverInput"
          >
            Edit
          </div>
        </div>
        <form @submit.prevent="submitCover()">
          <div class="mt-2">
            <input
              type="file"
              accept=".jpg, .jpeg, .png, .webp"
              @change="handleCoverPreview"
              @input="coverForm.cover = $event.target.files[0]"
              ref="coverInputRef"
              class="hidden"
            />
            <div
              class="relative aspect-[3/1] max-w-6xl overflow-hidden rounded-xl"
              v-if="coverPreview"
            >
              <img
                :src="coverPreview"
                class="object-cover w-full h-full"
                v-if="coverPreview"
              />
            </div>
            <div
              class="relative aspect-[3/1] max-w-6xl mx-auto overflow-hidden rounded-xl bg-black"
              v-else
            />
            <div class="flex">
              <button
                class="btn btn-primary mx-auto my-4 normal-case"
                v-if="!coverError && coverForm.cover !== auth.user.cover"
                :disabled="coverForm.processing"
              >
                Save
              </button>
            </div>
          </div>
        </form>
      </div>
    </div>
  </div>
</template>
<script setup lang="ts">
import { useForm } from '@inertiajs/vue3'
import { ref } from 'vue'
import type { AuthType } from '@/AuthType'

const { auth } = defineProps<{
  auth: AuthType
}>()

/**
 *  * Name
 */

const nameForm = useForm({
  firstname: auth.user.firstname,
  surname: auth.user.surname,
})

const submitName = () => {
  nameForm.post('/api/user/update', {
    onSuccess: () => {
      showNameSubmit.value = false
    },
  })
}

const showNameSubmit = ref<boolean>(false)

const handleName = () => {
  if (
    nameForm.firstname !== auth.user.firstname ||
    nameForm.surname !== auth.user.surname
  ) {
    showNameSubmit.value = true
  }
}

/**
 *  * Avatar
 */

const avatarForm = useForm({
  avatar: auth.user.avatar,
})

const submitAvatar = () => {
  avatarForm.post('/api/user/update', {
    onSuccess: () => {
      avatarForm.avatar = auth.user.avatar
    },
  })
}

const avatarInputRef = ref<HTMLElement | null>(null)
const avatarError = ref<boolean>(false)
const avatarPreview = ref<any>(auth.user.avatar)

const openAvatarInput = () => {
  avatarInputRef.value.click()
}

const handleAvatarPreview = (e) => {
  const inputElement = e.target
  const file = inputElement.files[0]

  if (file && isValidImageFile(file)) {
    const reader = new FileReader()

    reader.onload = () => {
      avatarPreview.value = reader.result
    }

    avatarError.value = false
    reader.readAsDataURL(file)
  } else {
    avatarForm.avatar = auth.user.avatar
    avatarPreview.value = auth.user.avatar
    avatarError.value = true
  }
}

/**
 *  * Cover
 */

const coverForm = useForm({
  cover: auth.user.cover,
})

const submitCover = () => {
  coverForm.post('/api/user/update', {
    onSuccess: () => {
      coverForm.cover = auth.user.cover
    },
  })
}

const coverInputRef = ref<HTMLElement | null>(null)
const coverError = ref<boolean>(false)
const coverPreview = ref<any>(auth.user.cover)

const openCoverInput = () => {
  coverInputRef.value.click()
}

const handleCoverPreview = (e) => {
  const inputElement = e.target
  const file = inputElement.files[0]

  if (file && isValidImageFile(file)) {
    const reader = new FileReader()

    reader.onload = () => {
      coverPreview.value = reader.result
    }

    coverError.value = false
    reader.readAsDataURL(file)
  } else {
    coverForm.cover = auth.user.cover
    coverPreview.value = auth.user.cover
    coverError.value = true
  }
}

const isValidImageFile = (file) => {
  const allowedExtensions = ['.jpg', '.jpeg', '.png', '.webp']
  const fileExtension = file.name
    .toLowerCase()
    .substring(file.name.lastIndexOf('.'))

  return allowedExtensions.includes(fileExtension)
}
</script>
