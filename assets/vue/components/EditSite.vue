<template>
  <div class="container">
    <div class="editSite">
      <FormResponse :is-loading="isLoading"
                    :success="success"
                    :error="error"
      />
      <form method="post" v-on:submit="submitForm" :action="this.formUrl">
        <input type="text" name="name" class="editSite__name" v-model="name">
        <div class="inputRow">
          <label for="url">URL</label>
          <input id="url" name="url" type="text" v-model="url">
        </div>
        <div class="inputRow">
          <label for="cms">CMS</label>
          <CmsInput id="cms"
                    :cms="cms"
                    v-on:update="this.updateCms"
          />
        </div>
        <div class="inputRow">
          <label for="admin-url">Admin URL</label>
          <input id="admin-url" name="admin-url" type="text" v-model="adminUrl">
        </div>
        <input type="hidden" name="id" :value="this.site.id">
        <input type="hidden" name="token" :value="csrfToken">
        <button type="submit">
          <i class="ion-md-save"></i>
          <span>Save</span>
        </button>
      </form>
    </div>
  </div>
</template>

<script>
  import axios from 'axios';
  import FormResponse from './FormResponse';
  import CmsInput from './Site/InputFields/CmsInput';

  export default {
    name: 'editSite',

    props: [
      'site',
      'formUrl',
      'csrfToken',
      'id',
      'redirectUrl'
    ],

    components: {
      CmsInput,
      FormResponse
    },

    data() {
      return {
        name: this.site.name,
        url: this.site.url,
        cms: this.site.cms,
        adminUrl: this.site.adminUrl,
        isLoading: false,
        error: null,
        success: null
      }
    },

    methods: {
      updateCms(cms) {
        this.cms = cms;
      },
      submitForm(e) {
        e.preventDefault();
        this.errorMessage = null;
        const formUrl = e.target.action;
        this.isLoading = true;
        axios.post(formUrl, {
          id: this.id,
          name: this.name,
          url: this.url,
          cms: this.cms,
          admin_url: this.adminUrl,
          token: this.csrfToken
        }).then(res => {
          if (res.data.response) {
            this.isLoading = false;
            this.success = 'Site successfully saved.';
            window.location.href = this.redirectUrl;
          }
        }).catch(error => {
          this.isLoading = false;
          this.password = null;
          if (error.response.data.error) {
            this.error = error.response.data.error;
          } else {
            this.error = error;
          }
        });
      }
    }
  }
</script>
