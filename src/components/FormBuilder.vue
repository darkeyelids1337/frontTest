<template>
  <div class="form-builder">
    <!-- Должно собираться автоматически, а не руками как сейчас, согласно конфигу   -->
    <form
      @submit.prevent="onSubmit"
      ref="form"
      v-for="item in items"
      :key="item.name"
      :id="item.name"
    >
      <div>
        <h1>{{ item.name }}</h1>
        <div v-for="data in item?.items" key="parent">
          <component
            :is="`form-${data.type}`"
            :label="data.label"
            :name="data.name"
            :options="data?.additional?.options"
          ></component>
        </div>
        <button type="submit">Отправить</button>
        <button type="reset">Стереть</button>
      </div>
    </form>
  </div>
</template>

<script>
import FormInput from "@/components/form-items/FormInput.vue";
import FormSelect from "@/components/form-items/FormSelect.vue";
import FormRadio from "@/components/form-items/FormRadio.vue";
import FormPassword from "@/components/form-items/FormPassword.vue";
export default {
  name: "FormBuilder",
  data() {
    return {
      items: [],
      formData: {
        parent: {},
        child: {},
      },
      password: "",
      repeatPassword: "",
    };
  },
  methods: {
    onSubmit(e) {
      const pass = e.target.elements.pass.value;
      const repeatPass = e.target.elements[`repeat-pass`].value;
      if (pass === repeatPass) {
        if (Object.keys(this.formData.parent).length !== 0) {
          this.formData.child = {
            name: e.target.elements.name.value,
            gender: e.target.elements[1].value,
            age: e.target.elements.radio.value,
            pass: e.target.elements.pass.value,
          };
          fetch("localhost:3000/fake", {
            method: "POST",
            body: JSON.stringify(this.formData),
          }).then(() => alert("submit")).catch(() => alert('submit'));
        } 
        else if (Object.keys(this.formData.child).length !== 0){
          this.formData.parent = {
            name: e.target.elements.name.value,
            gender: e.target.elements[1].value,
            age: e.target.elements.radio.value,
            pass: e.target.elements.pass.value,
          };
          fetch("localhost:3000/fake", {
            method: "POST",
            body: JSON.stringify(this.formData),
          }).then(() => alert("submit")).catch(() => alert('submit'));
        }
        else if (e.target.id === "Родитель") {
          this.formData.parent = {
            name: e.target.elements.name.value,
            gender: e.target.elements[1].value,
            age: e.target.elements.radio.value,
            pass: e.target.elements.pass.value,
          };
        } else {
          this.formData.child = {
            name: e.target.elements.name.value,
            gender: e.target.elements[1].value,
            age: e.target.elements.radio.value,
            pass: e.target.elements.pass.value,
          };
        }
      } else {
        alert("diff password!");
        this.$refs.form.reset();
      }
    },
  },
  created() {
    fetch("../form-config.json")
      .then((res) => res.json())
      .then((items) => {
        this.items = items;
      });
  },
  components: { FormPassword, FormRadio, FormSelect, FormInput },
};
</script>

<style scoped>
.form-builder {
  display: flex;
  flex-wrap: wrap;
  margin: 0 auto;
  align-items: center;
  justify-content: space-around;
  width: 100%;
  max-width: 1200px;
}
.form-builder__buttons {
  width: 100%;
  margin-top: 5%;
  text-align: center;
}
</style>
