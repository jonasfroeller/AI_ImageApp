<script setup lang="ts">
    import { object, string, type InferType } from 'yup'
    import type { FormSubmitEvent } from '#ui/types'
    import { ref, reactive } from 'vue'

    const image = ref<string | undefined>(undefined)
    const isLoading = ref<boolean>(false)
    const timestamp = Date.now().toString()
    const filename = ref<string | undefined>(undefined)
    const imageBlob = ref<undefined | Blob>(undefined)

    const schema = object({
        input: string().min(2).required('Required')
    })

    type Schema = InferType<typeof schema>

    const state = reactive({
        input: undefined,
    })

    function onSubmit (event: FormSubmitEvent<Schema>) {
        const API_URL = `https://imaginary.publik-froeller-jonas2620.workers.dev?text=${event.data.input}`
        image.value = API_URL
        isLoading.value = true
    }

    async function dataURItoBlob() {
        const response = await fetch(image.value as string)
        const blob = await response.blob()
        return blob
    }
 
    async function onImageLoad() {
        isLoading.value = false
        filename.value = `generated-${timestamp}.png`

        imageBlob.value = await dataURItoBlob()
        console.log("image", imageBlob.value)
    }

    function downloadImage() {
      if (!imageBlob.value) {
        alert('failed to download image')
        return;
      }

      const url = URL.createObjectURL(imageBlob.value)
      const link = document.createElement('a')
      link.href = url
      link.download = filename.value ?? "generated.png"
      link.target = '_blank'
      link.click();
      URL.revokeObjectURL(url)

      console.log("image url", url)
    }
</script>

<template>
    <div class="flex gap-1">
        <UForm :schema="schema" :state="state" class="space-y-2" @submit="onSubmit">
            <UFormGroup label="Prompt" name="input">
                <UTextarea v-model="state.input" />
            </UFormGroup>
            <UButton type="submit">
                Submit
            </UButton>
        </UForm>
    </div>
    <div class="pt-6">
        <div v-if="isLoading">
            <UButton loading>loading...</UButton>
            <USkeleton class="h-[1000px] w-[1000px]" />
        </div>
        <div v-else-if="image === undefined">
            (your generated image will appear here)
        </div>
        <div v-show="!isLoading && image !== undefined">
            <img :src="image" @load="onImageLoad" />
            <UButton @click="downloadImage">
                download
            </UButton>
        </div>
    </div>
</template>