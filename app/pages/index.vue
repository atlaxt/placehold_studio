<script setup lang="ts">
const open = ref(true)
const width = ref('600')
const height = ref('400')
const format = ref('svg')
const bgColor = ref('#e2e8f0')
const bgInput = ref('#e2e8f0')
const textColor = ref('#64748b')
const textInput = ref('#64748b')

watch(bgColor, v => bgInput.value = v)
watch(textColor, v => textInput.value = v)

function onBgInput(v: string) {
  bgInput.value = v
  if (/^#[0-9a-f]{6}$/i.test(v))
    bgColor.value = v
}

function onTextInput(v: string) {
  textInput.value = v
  if (/^#[0-9a-f]{6}$/i.test(v))
    textColor.value = v
}
const customText = ref('')
const font = ref('Lato')
const retina = ref('1x')
const copied = ref(false)
const toast = useToast()

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
  toast.add({ title: 'URL copied!', color: 'success', icon: 'i-lucide-check' })
}
</script>

<template>
  <div class="flex h-svh overflow-hidden">
    <USidebar v-model:open="open" :style="{ '--sidebar-width': '25rem' }" collapsible="offcanvas">
      <div class="p-4 space-y-5 overflow-y-auto">
        <UFormField label="Size" help="Enter a value between 10 and 4000">
          <UFieldGroup class="w-full">
            <UInputNumber
              :model-value="Number.parseInt(width)"
              :min="10"
              :max="4000"
              placeholder="Width"
              class="w-full"
              @update:model-value="(v) => width = v.toString()"
            />
            <UBadge label="x" color="neutral" variant="subtle" />
            <UInputNumber
              :model-value="Number.parseInt(height)"
              :min="10"
              :max="4000"
              placeholder="Height"
              class="w-full"
              @update:model-value="(v) => height = v.toString()"
            />
          </UFieldGroup>
        </UFormField>

        <UFormField label="Format" class="w-full">
          <USelect v-model="format" :items="formats" class="w-full" />
        </UFormField>

        <UFormField label="Background Color" class="w-full">
          <UPopover>
            <UFieldGroup class="w-full">
              <UButton class="w-8" :style="{ backgroundColor: bgColor }" />
              <UInput class="w-full" :model-value="bgInput" placeholder="#e2e8f0" @update:model-value="onBgInput" />
            </UFieldGroup>
            <!-- <UInput v-model="bgColor" placeholder="#e2e8f0" class="w-full font-mono">
              <template #leading>
                <span :style="{ backgroundColor: bgColor }" class="size-3 rounded-full" />
              </template>
            </UInput> -->
            <template #content>
              <UColorPicker v-model="bgColor" class="p-2" />
            </template>
          </UPopover>
        </UFormField>

        <UFormField label="Text Color" class="w-full">
          <UPopover>
            <UFieldGroup class="w-full">
              <UButton class="w-8" :style="{ backgroundColor: textColor }" />
              <UInput class="w-full" :model-value="textInput" placeholder="#64748b" @update:model-value="onTextInput" />
            </UFieldGroup>
            <template #content>
              <UColorPicker v-model="textColor" class="p-2" />
            </template>
          </UPopover>
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
        <div class="flex flex-col gap-2 w-full">
          <div class="flex items-center justify-between">
            <div class="flex items-center gap-1.5 text-xs text-gray-400">
              <span>made by</span>
              <a href="https://atlaxt.me" target="_blank">
                <UColorModeImage
                  light="https://atlaxt.me/sign_black.png"
                  dark="https://atlaxt.me/sign_white.png"
                  class="h-5 w-auto"
                  alt="atlaxt"
                />
              </a>
            </div>
            <div class="flex items-center gap-1">
              <UButton
                icon="i-simple-icons-github"
                color="neutral"
                variant="ghost"
                size="xs"
                to="https://github.com/atlaxt/placehold_studio"
                target="_blank"
                aria-label="GitHub"
              />
              <UColorModeButton size="xs" />
            </div>
          </div>
          <p class="text-xs text-gray-400/60">
            powered by <a href="https://placehold.co" target="_blank" class="underline underline-offset-2 hover:text-gray-400 transition-colors">placehold.co</a>
          </p>
        </div>
      </template>
    </USidebar>

    <div class="flex flex-col flex-1 min-w-0">
      <div class="h-(--ui-header-height) shrink-0 flex items-center gap-2 px-4 border-b border-default">
        <UButton
          icon="i-lucide-panel-left"
          color="neutral"
          variant="ghost"
          aria-label="Toggle sidebar"
          @click="open = !open"
        />
        <code class="flex-1 text-xs text-gray-500 dark:text-gray-400 truncate cursor-pointer" @click="copyUrl">{{ placeholdUrl }}</code>
        <UButton
          size="xs"
          variant="ghost"
          :icon="copied ? 'i-lucide-check' : 'i-lucide-copy'"
          :color="copied ? 'success' : 'neutral'"
          @click="copyUrl"
        />
      </div>

      <div
        class="flex-1 flex items-center justify-center p-8 overflow-hidden"
        style="background-image: repeating-conic-gradient(#80808015 0% 25%, transparent 0% 50%); background-size: 20px 20px;"
      >
        <img
          :src="placeholdUrl"
          :alt="`${width}x${height} placeholder`"
          class="max-w-full max-h-full shadow-xl rounded"
        >
      </div>
    </div>
  </div>
</template>
