<script setup lang="ts">
const width = ref('600')
const height = ref('400')
const format = ref('svg')
const bgColor = ref('#e2e8f0')
const textColor = ref('#64748b')
const customText = ref('')
const font = ref('Lato')
const retina = ref('1x')
const copied = ref(false)

const formats = ['svg', 'png', 'jpeg', 'gif', 'webp', 'avif']
const fonts = ['Lato', 'Lora', 'Montserrat', 'Noto Sans', 'Open Sans', 'Oswald', 'Playfair Display', 'Poppins', 'PT Sans', 'Raleway', 'Roboto', 'Source Sans Pro']

const placeholdUrl = computed(() => {
  const w = Number.parseInt(width.value) || 600
  const h = Number.parseInt(height.value) || 400
  const bg = bgColor.value.replace('#', '')
  const fg = textColor.value.replace('#', '')

  let url = `https://placehold.co/${w}x${h}/${bg}/${fg}`
  if (retina.value !== '1x')
    url += `@${retina.value}`
  url += `.${format.value}`

  const params = new URLSearchParams()
  if (customText.value)
    params.set('text', customText.value.replace(/\n/g, '\\n'))
  if (font.value !== 'Lato')
    params.set('font', font.value)
  const qs = params.toString()
  if (qs)
    url += `?${qs}`

  return url
})

async function copyUrl() {
  await navigator.clipboard.writeText(placeholdUrl.value)
  copied.value = true
  setTimeout(() => (copied.value = false), 2000)
}
</script>

<template>
  <UDashboardGroup>
    <UDashboardSidebar collapsible resizable :default-size="22" :min-size="18" :max-size="35">
      <div class="p-4 space-y-5 overflow-y-auto">
        <UFormField
          label="Size"
          help="Enter a value between 10 and 4000"
        >
          <UFieldGroup class="w-full">
            <UInput v-model="width" type="number" min="10" max="4000" placeholder="Width" class="w-full" />
            <UBadge label="x" color="neutral" variant="subtle" />
            <UInput v-model="height" type="number" min="10" max="4000" placeholder="Height" class="w-full" />
          </UFieldGroup>
        </UFormField>

        <UFormField label="Format">
          <USelect v-model="format" :items="formats" class="w-full" />
        </UFormField>

        <UFormField label="Background Color">
          <div class="flex items-center gap-2">
            <UPopover>
              <UButton
                size="sm"
                variant="outline"
                class="w-9 h-9 p-0 shrink-0"
                :style="`background-color: ${bgColor}`"
              />
              <template #content>
                <UColorPicker v-model="bgColor" />
              </template>
            </UPopover>
            <UInput v-model="bgColor" placeholder="#e2e8f0" class="flex-1 font-mono" />
          </div>
        </UFormField>

        <UFormField label="Text Color">
          <div class="flex items-center gap-2">
            <UPopover>
              <UButton
                size="sm"
                variant="outline"
                class="w-9 h-9 p-0 shrink-0"
                :style="`background-color: ${textColor}`"
              />
              <template #content>
                <UColorPicker v-model="textColor" />
              </template>
            </UPopover>
            <UInput v-model="textColor" placeholder="#64748b" class="flex-1 font-mono" />
          </div>
        </UFormField>

        <UFormField label="Custom Text">
          <UTextarea v-model="customText" placeholder="Enter text..." class="w-full" :rows="3" />
        </UFormField>

        <UFormField label="Font">
          <USelect v-model="font" :items="fonts" class="w-full" />
        </UFormField>

        <UFormField label="Retina">
          <USelect v-model="retina" :items="['1x', '2x', '3x']" class="w-full" />
        </UFormField>
      </div>

      <template #footer>
        <UColorModeButton />
      </template>
    </UDashboardSidebar>

    <UDashboardPanel class="flex-1">
      <div class="h-full flex flex-col">
        <div class="px-4 py-3 border-b border-gray-200 dark:border-gray-800 flex items-center gap-2">
          <code class="flex-1 text-xs text-gray-500 dark:text-gray-400 truncate select-all">{{ placeholdUrl }}</code>
          <UButton
            size="xs"
            variant="ghost"
            :icon="copied ? 'i-lucide-check' : 'i-lucide-copy'"
            :color="copied ? 'success' : 'neutral'"
            @click="copyUrl"
          />
        </div>

        <div
          class="flex-1 flex items-center justify-center p-8"
          style="background-image: repeating-conic-gradient(#80808015 0% 25%, transparent 0% 50%); background-size: 20px 20px;"
        >
          <img
            :src="placeholdUrl"
            :alt="`${width}x${height} placeholder`"
            class="max-w-full max-h-full shadow-xl rounded"
          >
        </div>
      </div>
    </UDashboardPanel>
  </UDashboardGroup>
</template>
