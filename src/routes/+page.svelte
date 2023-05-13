
<script>
  // import { goto } from "$app/navigation";
  import supabase from "$lib/db";
  import { user } from "$lib/stores";
  import { goto } from "$app/navigation";
  let email = "";


  const signUp = async () => {
   
let {  user: userDetails, error } = await supabase.auth.signUp({
  email: email,
  password: 'orxNPdjehPXsKyXBQFdt'
});

    $user = userDetails;
    goto("/");
  };

  const logIn = async () => {
    
let {  user: userDetails, error } = await supabase.auth.signIn({
  email: email,
  password: 'orxNPdjehPXsKyXBQFdt'
});
$user = userDetails;
    goto("/login");

  };
</script>

<style>
  .body {
    display: flex;
    justify-content: center;
    align-items: center;
    height: 100vh;
  }

  .card {
    background-color: white;
    border-radius: 10px;
    box-shadow: 0 0 10px rgba(0, 0, 0, 0.2);
    max-width: 400px;
    margin: 0 auto;
    padding: 20px;
  }



  input {
    border: 1px solid gray;
    border-radius: 5px;
    padding: 5px;
    width: 100%;
  }

  button {
    margin-top: 1rem;
    padding: 0.5rem 1rem;
    border: none;
    border-radius: 4px;
    background-color: blue;
    color: white;
    font-size: 1rem;
    cursor: pointer;
  }

  button:hover {
    background-color: #0779c5;
  }
</style>
<div class="body">
<div class="card">
  <h1>Login</h1>

    <input bind:value={email} type="email" id="email" placeholder="email@example.com" />

  <button on:click={logIn}>Login</button>
  <button on:click={signUp}>Sign Up</button>
</div>
</div>

