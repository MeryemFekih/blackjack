<script lang="ts">
  import { goto } from "$app/navigation";

  async function getUser() {
    const res = await fetch('http://127.0.0.1:8888/user/profile', {
      method: 'GET',
      headers: {
        'Content-Type': 'application/json',
        'Authorization': 'Bearer ' + (localStorage.getItem('token') || '')
      }
    });

    if (res.status === 401) {
      localStorage.removeItem('token');
      goto('/');
      return null;
    }

    return await res.json();
  }

  const userPromise = getUser();
</script>

{#await userPromise}
  <p>Loading...</p>
{:then user}
  {#if user}
    <h1 class="h1">Welcome {user.username} !</h1>
    <div class="flex flex-col w-2/3 p-6 ">

      <div class="flex justify-around items-center my-6">
        <label class="text-primary-foreground justify-items-start w-1/2" for="username">Username:</label>
        <input class="border justify-items-end w-1/2 py-1 rounded text-black" type="text" id="username" name="username" value={user.username} disabled/>
      </div>
      <div class="flex justify-around items-center my-6">
        <label class="text-primary-foreground justify-items-start w-1/2" for="email">Email:</label>
        <input class="border justify-items-end w-1/2 py-1 rounded text-black" type="email" id="email" name="email" value={user.email} disabled/>
      </div>

      <div class="flex justify-around items-center my-6">
        <label class="text-primary-foreground justify-items-start w-1/2" for="wallet">Wallet:</label>
        <input class="border justify-items-end w-1/2 py-1 rounded text-black" type="number" id="wallet" name="wallet" value={user.wallet} disabled/>
      </div>

    </div>
  {:else}
    <p>Redirecting...</p>
  {/if}
{:catch error}
  <p class="text-red-500">Error loading profile: {error.message}</p>
{/await}