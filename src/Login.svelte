<script>
  import { onMount } from 'svelte'
  import { navigate } from 'svelte-routing'
  import { userStore } from './store'
  import decode from 'jwt-decode'

  let user
  userStore.subscribe(value => {
    user = value
  })

  onMount(() => {
    if (user) navigate('/')
  })

  const login = async (event) => {
    event.preventDefault()
    const form = document.getElementById('login')
    const formData = new FormData(form)
    form.reset()

    const url = 'API_BASE_URL/api/login'
    const res = await fetch(url, {
      method: 'POST',
      headers: {
        'Content-Type': 'application/json'
      },
      body: JSON.stringify({
        username: formData.get('username'),
        password: formData.get('password')
      })
    })

    if (!res.ok) alert('Invalid credentials')
    const { token } = await res.json()

    try {
      userStore.update(() => decode(token).user)
      localStorage.setItem('token', token)
    } catch (error) {
      console.log(error)
    }

    return navigate('/')
  }
</script>

<div class="row">
  <div class="column column-33">
    <form id="login">
      <fieldset>
        <input type="text" placeholder="Username" id="username" name="username">
        <input type="text" placeholder="Password" id="password" name="password">

        <button class="button-primary float-right" type="submit" on:click={ login }>Login</button>
      </fieldset>
    </form>
  </div>
</div>