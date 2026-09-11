<!-- src/routes/login/+page.svelte -->
<script>
  import { goto } from '$app/navigation';
  import { api } from '$lib/api.js';
  import { setAuth } from '$lib/auth.js';

  let email = '';
  let password = '';
  let loading = false;
  let error = '';

  async function handleSubmit(e) {
    e.preventDefault();
    error = '';
    loading = true;
    try {
      const { token } = await api.login(email, password);
      setAuth(token, null);
      const user = await api.getUser();
      setAuth(token, user);
      goto('/');
    } catch (err) {
      error = err.message || 'Falha no login';
    } finally {
      loading = false;
    }
  }
</script>

<div class="min-h-screen flex items-center justify-center bg-gradient-to-br from-slate-900 to-slate-800 p-4">
  <form
    on:submit={handleSubmit}
    class="bg-white p-8 rounded-xl w-full max-w-sm shadow-2xl flex flex-col gap-4"
  >
    <div class="text-4xl text-center">🎓</div>
    <h1 class="text-center text-xl font-bold text-slate-900 m-0">Sistema Acadêmico</h1>
    <p class="text-center text-slate-500 text-sm -mt-2 mb-1">Faça login para continuar</p>

    {#if error}
      <div class="bg-red-100 text-red-700 px-3 py-2 rounded-md text-sm">{error}</div>
    {/if}

    <label class="flex flex-col gap-1 text-sm text-slate-700">
      <span>E-mail</span>
      <input
        type="email"
        bind:value={email}
        required
        autocomplete="username"
        class="px-3 py-2 border border-slate-300 rounded-md text-sm
          focus:outline-none focus:ring-2 focus:ring-sky-400 focus:border-sky-400"
      />
    </label>

    <label class="flex flex-col gap-1 text-sm text-slate-700">
      <span>Senha</span>
      <input
        type="password"
        bind:value={password}
        required
        autocomplete="current-password"
        class="px-3 py-2 border border-slate-300 rounded-md text-sm
          focus:outline-none focus:ring-2 focus:ring-sky-400 focus:border-sky-400"
      />
    </label>

    <button
      type="submit"
      disabled={loading}
      class="mt-2 py-2.5 bg-blue-600 hover:bg-blue-700 text-white rounded-md font-semibold
        text-sm transition disabled:opacity-70 disabled:cursor-not-allowed"
    >
      {loading ? 'Entrando...' : 'Entrar'}
    </button>
  </form>
</div>