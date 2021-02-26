<template>
  <div>
    <nav class="navbar navbar-expand-lg navbar-light bg-light">
      <div class="container-fluid">
        <nuxt-link
          v-if="!token"
          class="navbar-brand"
          aria-current="page"
          to="/"
        >
          Accueil </nuxt-link
        ><nuxt-link
          v-else
          class="navbar-brand"
          aria-current="page"
          to="/commandes"
        >
          Accueil
        </nuxt-link>
        <button
          class="navbar-toggler"
          type="button"
          data-bs-toggle="collapse"
          data-bs-target="#navbarSupportedContent"
          aria-controls="navbarSupportedContent"
          aria-expanded="false"
          aria-label="Toggle navigation"
        >
          <span class="navbar-toggler-icon"></span>
        </button>
        <div class="collapse navbar-collapse" id="navbarSupportedContent">
          <ul v-if="roleChecked" class="navbar-nav me-auto mb-2 mb-lg-0">
            <li class="nav-item">
              <b-dropdown
                class="nav-link active"
                split
                split-to="/produits"
                text="Produits"
              >
                <b-dropdown-item to="/categories">Catégories</b-dropdown-item>
                <b-dropdown-item to="/attributs">Attributs</b-dropdown-item>
              </b-dropdown>
            </li>

            <li class="nav-item">
              <nuxt-link
                class="nav-link active"
                aria-current="page"
                to="/commandes"
                >Commandes</nuxt-link
              >
            </li>
            <li class="nav-item">
              <nuxt-link
                class="nav-link active"
                aria-current="page"
                to="/clients"
                >Clients</nuxt-link
              >
            </li>
            <li class="nav-item">
              <button
                class="nav-link active"
                aria-current="page"
                @click="logout"
              >
                Deconnexion
              </button>
            </li>
          </ul>
        </div>
      </div>
    </nav>
    <script
      src="https://cdn.jsdelivr.net/npm/bootstrap@5.0.0-beta1/dist/js/bootstrap.bundle.min.js"
      integrity="sha384-ygbV9kiqUc6oa4msXn9868pTtWMgiQaeYH7/t7LECLbyPA2x65Kgf80OJFdroafW"
      crossorigin="anonymous"
    ></script>
    <Nuxt />
  </div>
</template>
<script>
export default {
  data() {
    return {
      username: localStorage.getItem("username"),
      token: localStorage.getItem("token"),
      roleChecked: false,
    };
  },
  mounted() {
    window.addresse = "http://applicommande.local/"
    this.roleChecking();
  },
  methods: {
    roleChecking() {
      let role = localStorage.getItem("role");

      if (role === "administrator") {
        this.roleChecked = true;
      } else {
        this.roleChecked = false;
      }
    },
    logout() {
      localStorage.clear();
      window.location.href = "/";
    },
  },
};
</script>
<style>
* {
  padding: 0;
  margin: 0;
  box-sizing: border-box;
}
</style>
