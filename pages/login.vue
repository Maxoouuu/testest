<script setup lang="ts">
const { auth } = useSupabaseAuthClient();
const user = useSupabaseUser();
const router = useRouter();

/* const password = ref("");
const email = ref("");
const errorMsg = ref("");
const login = async () => {
  const loginRequest = {
    email: email.value,
    password: password.value,
  };

  const { data: response, error } = await auth.signInWithPassword(loginRequest);
  if (!!response) {
    const accessToken = useCookie("sb-access-token");
    const refreshToken = useCookie("sb-refresh-token");
    accessToken.value = response.session?.access_token ?? null;
    refreshToken.value = response.session?.refresh_token ?? null;
  }

  if (!!error) {
    errorMsg.value = error.message;
  }
}; */

const supabase = useSupabaseClient();

const loading = ref(false);
const email = ref("");
const handleLogin = async () => {
  try {
    loading.value = true;
    const { error } = await supabase.auth.signInWithOtp({ email: email.value });
    if (error) throw error;
    alert("Check your email for the login link!");
  } catch (error) {
    alert(error.error_description || error.message);
  } finally {
    loading.value = false;
  }
};

watch(user, () => {
  if (!!user.value) {
    router.push("/");
  }
});

const testAPI = async () => {
  const { data } = await useFetch("/api/test", {
    headers: useRequestHeaders(["cookie"]),
  });
  alert(data.value);
};
</script>
<template>
  <div>
    <label>E-Mail: </label>
    <input type="text" v-model="email" />
  </div>
  <!-- <div>
    <label>Password: </label>
    <input type="password" v-model="password" />
  </div> -->
  <button @click="handleLogin">Login</button>
  <button @click="testAPI()">Test API Call</button>
  <!-- <div>{{ errorMsg }}</div> -->
</template>
