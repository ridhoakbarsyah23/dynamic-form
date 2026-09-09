<script setup>
import { computed, nextTick, ref } from 'vue'

let nextId = 2

const makeEntry = () => ({
  id: nextId++,
  name: '',
  address: '',
  touched: { name: false, address: false },
})

const entries = ref([
  {
    id: 1,
    name: '',
    address: '',
    touched: { name: false, address: false },
  },
])
const submittedEntries = ref([])
const formMessage = ref('')
const saveNotice = ref('')
const resultSection = ref(null)

const validEntries = computed(() =>
  entries.value.filter(
    (entry) => entry.name.trim().length >= 3 && entry.address.trim().length >= 10,
  ).length,
)

const completionPercent = computed(() => {
  const totalFields = entries.value.length * 2
  const completedFields = entries.value.reduce(
    (total, entry) =>
      total + Number(entry.name.trim().length >= 3) + Number(entry.address.trim().length >= 10),
    0,
  )
  return Math.round((completedFields / totalFields) * 100)
})

function isEntryValid(entry) {
  return entry.name.trim().length >= 3 && entry.address.trim().length >= 10
}

function fieldError(entry, field) {
  if (!entry.touched[field]) return ''

  const value = entry[field].trim()
  if (!value) return field === 'name' ? 'Nama wajib diisi.' : 'Alamat wajib diisi.'
  if (field === 'name' && value.length < 3) return 'Nama minimal 3 karakter.'
  if (field === 'address' && value.length < 10) return 'Alamat minimal 10 karakter.'
  return ''
}

function clearMessages() {
  formMessage.value = ''
  saveNotice.value = ''
}

async function addEntry() {
  formMessage.value = ''
  saveNotice.value = ''
  entries.value.push(makeEntry())
  await nextTick()
  document.querySelector(`[data-entry-id="${entries.value.at(-1).id}"] input`)?.focus()
}

function removeEntry(id) {
  if (entries.value.length === 1) return
  entries.value = entries.value.filter((entry) => entry.id !== id)
  formMessage.value = ''
  saveNotice.value = ''
}

async function submitForm() {
  entries.value.forEach((entry) => {
    entry.touched.name = true
    entry.touched.address = true
  })

  if (validEntries.value !== entries.value.length) {
    formMessage.value = 'Periksa kembali kolom yang masih belum lengkap.'
    saveNotice.value = ''
    nextTick(() => document.querySelector('.field-control[aria-invalid="true"]')?.focus())
    return
  }

  submittedEntries.value = entries.value.map(({ name, address }) => ({
    name: name.trim(),
    address: address.trim(),
  }))
  formMessage.value = ''
  saveNotice.value = 'Data berhasil tersimpan.'

  await nextTick()
  resultSection.value?.scrollIntoView({ behavior: 'smooth', block: 'nearest' })
}

function resetForm() {
  entries.value = [makeEntry()]
  submittedEntries.value = []
  formMessage.value = ''
  saveNotice.value = ''
  nextTick(() => document.querySelector('.field-control')?.focus())
}
</script>

<template>
  <main class="page-shell">
    <section class="intro-panel" aria-labelledby="page-title">
      <a class="brand" href="#" aria-label="Beranda Formly">
        <span class="brand-mark" aria-hidden="true">
          <svg viewBox="0 0 24 24" fill="none">
            <path d="M7 7.5h10M7 12h6M7 16.5h4" />
          </svg>
        </span>
        <span>formly.</span>
      </a>

      <div class="intro-copy">
        <p class="eyebrow">DATA PENGGUNA</p>
        <h1 id="page-title">Satu form,<br /><em>semua data.</em></h1>
        <p class="intro-text">
          Tambahkan satu atau beberapa data pengguna dengan mudah. Sederhana, cepat,
          dan tertata rapi.
        </p>

        <div class="trust-note">
          <span class="trust-avatars" aria-hidden="true">
            <i>AS</i><i>DN</i><i>+</i>
          </span>
          <p><strong>Mudah digunakan</strong><small>Form intuitif, tanpa langkah rumit</small></p>
        </div>

        <div class="steps" aria-label="Tahapan pengisian">
          <div class="step active">
            <span>01</span>
            <div><strong>Isi data</strong><small>Nama dan alamat lengkap</small></div>
          </div>
          <div class="step">
            <span>02</span>
            <div><strong>Periksa kembali</strong><small>Pastikan data sudah benar</small></div>
          </div>
          <div class="step">
            <span>03</span>
            <div><strong>Simpan data</strong><small>Selesai dalam hitungan detik</small></div>
          </div>
        </div>
      </div>

      <p class="copyright">© 2026 Formly.</p>
    </section>

    <section class="form-panel" aria-labelledby="form-title">
      <div class="form-container">
        <header class="form-header">
          <div>
            <p class="eyebrow">Form</p>
            <h2 id="form-title">Data pengguna</h2>
            <p>Lengkapi informasi berikut ini.</p>
          </div>
          <div class="entry-count" aria-live="polite">
            <strong>{{ entries.length }}</strong>
            <span>{{ entries.length === 1 ? 'entri' : 'entri' }}</span>
          </div>
        </header>

        <div class="progress-block" aria-label="Progres pengisian form">
          <div class="progress-copy">
            <span>Progres pengisian</span>
            <strong>{{ completionPercent }}%</strong>
          </div>
          <div
            class="progress-track"
            role="progressbar"
            aria-valuemin="0"
            aria-valuemax="100"
            :aria-valuenow="completionPercent"
          >
            <span :style="{ width: `${completionPercent}%` }"></span>
          </div>
        </div>

        <form novalidate @submit.prevent="submitForm">
          <div class="entry-list">
            <article
              v-for="(entry, index) in entries"
              :key="entry.id"
              class="entry-card"
              :class="{ completed: isEntryValid(entry) }"
              :data-entry-id="entry.id"
            >
              <div class="entry-heading">
                <div class="entry-number">{{ String(index + 1).padStart(2, '0') }}</div>
                <h3>Pengguna {{ index + 1 }}</h3>
                <span v-if="isEntryValid(entry)" class="status-pill">
                  <svg viewBox="0 0 16 16" aria-hidden="true"><path d="m3 8 3 3 7-7" /></svg>
                  Lengkap
                </span>
                <button
                  v-if="entries.length > 1"
                  class="icon-button"
                  type="button"
                  :aria-label="`Hapus pengguna ${index + 1}`"
                  @click="removeEntry(entry.id)"
                >
                  <svg viewBox="0 0 24 24" aria-hidden="true">
                    <path d="M4 7h16M9 7V4h6v3m-8 0 1 13h8l1-13M10 11v5m4-5v5" />
                  </svg>
                </button>
              </div>

              <div class="fields-grid">
                <label class="field-group">
                  <span>Nama lengkap <b>*</b></span>
                  <span class="input-wrap">
                    <svg viewBox="0 0 24 24" aria-hidden="true">
                      <circle cx="12" cy="8" r="3.5" />
                      <path d="M5 20c.4-4.1 2.8-6.2 7-6.2s6.6 2.1 7 6.2" />
                    </svg>
                    <input
                      v-model="entry.name"
                      class="field-control"
                      type="text"
                      autocomplete="name"
                      placeholder="Contoh: Budi Santoso"
                      :aria-invalid="Boolean(fieldError(entry, 'name'))"
                      :aria-describedby="`name-error-${entry.id}`"
                      @blur="entry.touched.name = true"
                      @input="clearMessages"
                    />
                  </span>
                  <small v-if="fieldError(entry, 'name')" :id="`name-error-${entry.id}`" class="error-text">
                    {{ fieldError(entry, 'name') }}
                  </small>
                  <small v-else class="field-hint">Minimal 3 karakter</small>
                </label>

                <label class="field-group">
                  <span>Alamat lengkap <b>*</b></span>
                  <span class="input-wrap textarea-wrap">
                    <svg viewBox="0 0 24 24" aria-hidden="true">
                      <path d="M20 10c0 5.3-8 11-8 11S4 15.3 4 10a8 8 0 1 1 16 0Z" />
                      <circle cx="12" cy="10" r="2.5" />
                    </svg>
                    <textarea
                      v-model="entry.address"
                      class="field-control"
                      rows="3"
                      maxlength="200"
                      autocomplete="street-address"
                      placeholder="Nama jalan, nomor, kota, kode pos"
                      :aria-invalid="Boolean(fieldError(entry, 'address'))"
                      :aria-describedby="`address-error-${entry.id}`"
                      @blur="entry.touched.address = true"
                      @input="clearMessages"
                    ></textarea>
                  </span>
                  <small
                    v-if="fieldError(entry, 'address')"
                    :id="`address-error-${entry.id}`"
                    class="error-text"
                  >
                    {{ fieldError(entry, 'address') }}
                  </small>
                  <small v-else class="field-hint address-hint">
                    <span>Gunakan alamat yang mudah ditemukan</span>
                    <span>{{ entry.address.length }}/200</span>
                  </small>
                </label>
              </div>
            </article>
          </div>

          <button class="add-button" type="button" @click="addEntry">
            <span aria-hidden="true">+</span>
            Tambah pengguna lain
          </button>

          <p v-if="formMessage" class="form-error" role="alert">{{ formMessage }}</p>

          <div class="form-actions">
            <button class="reset-button" type="button" @click="resetForm">Reset</button>
            <button class="submit-button" type="submit">
              Simpan data
              <svg viewBox="0 0 24 24" aria-hidden="true">
                <path d="m5 12 14 0m-5-5 5 5-5 5" />
              </svg>
            </button>
          </div>
        </form>

        <div
          v-if="saveNotice"
          class="save-notification"
          role="status"
          aria-live="polite"
        >
          <div class="success-icon" aria-hidden="true">&#10003;</div>
          <div>
            <strong>{{ saveNotice }}</strong>
            <span>{{ submittedEntries.length }} data pengguna berhasil ditambahkan.</span>
          </div>
        </div>

        <section
          v-if="submittedEntries.length"
          ref="resultSection"
          class="result-card"
          aria-labelledby="saved-data-title"
        >
          <div class="result-content">
            <h3 id="saved-data-title">Data tersimpan</h3>
            <p>Berikut data pengguna yang baru saja disimpan.</p>
            <ul>
              <li v-for="(item, index) in submittedEntries" :key="index">
                <span class="saved-number">{{ index + 1 }}</span>
                <span class="saved-detail">
                  <strong>{{ item.name }}</strong>
                  <span>{{ item.address }}</span>
                </span>
              </li>
            </ul>
          </div>
        </section>
      </div>
    </section>
  </main>
</template>
