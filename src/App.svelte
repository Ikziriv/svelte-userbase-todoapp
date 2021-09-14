<script>
  import "./TailwindStyles.svelte";

  const message = "Learn Svelte";
  // Init
  let userObject = null;

  const userbase = window.userbase;
  let authPromise = userbase
    .init({ appId: "0c6c0e3f-d786-48fc-8624-6c3c19851518" })
    .then(({ user }) => (userObject = user));
  // Var Auth
  let username, password;

  const signIn = () =>
    (authPromise = userbase
      .signIn({ username, password })
      .then((user) => (userObject = user)));
  const signUp = () =>
    (authPromise = userbase
      .signUp({ username, password })
      .then((user) => (userObject = user)));
  const signOut = () =>
    (authPromise = userbase.signOut().then(() => (userObject = null)));

  // DB
  let todoPromise;
  let todos = [],
    newTodo = "";
  const databaseName = "todos";
  function changeHandler(items) {
    todos = items;
  }
  $: if (userObject)
    todoPromise = userbase.openDatabase({ databaseName, changeHandler });

  function addTodo() {
    todoPromise = userbase.insertItem({ databaseName, item: newTodo });
    newTodo = "";
  }

  function deleteTodo(itemId) {
    todoPromise = userbase.deleteItem({ databaseName, itemId });
  }

  function updateTodo(index) {
    const { item, itemId } = todos[index];
    todoPromise = userbase.updateItem({ databaseName, itemId, item });
  }
</script>

<main class="w-full h-screen relative">
  <nav
    class="font-sans bg-white text-center flex justify-between items-center py-6 mx-auto container overflow-hidden"
  >
    <a href="#">
      <img src="/logo.svg" class="h-10" alt="logo" />
    </a>
    <ul class="text-sm text-gray-700 list-none p-0 flex items-center">
      <li>
        <a
          href="#"
          class="inline-block font-medium tracking-widest uppercase text-xs py-2 px-3 text-gray-900 hover:text-gray-700 no-underline"
          >Todo Apps</a
        >
      </li>
    </ul>
  </nav>
  <section class="bg-white py-0 font-sans">
    <div
      class="container w-full m-auto flex flex-wrap md:flex-no-wrap items-center justify-between"
    >
      <div class="flex w-full justify-center md:justify-start">
        <div class="w-full py-6">
          {#await authPromise}
            <div class="bg-white w-full h-auto py-2 flex" disabled>
              <svg
                class="animate-spin -ml-1 mr-3 h-5 w-5 text-gray-700"
                xmlns="http://www.w3.org/2000/svg"
                fill="none"
                viewBox="0 0 24 24"
              >
                <circle
                  class="opacity-25"
                  cx="12"
                  cy="12"
                  r="10"
                  stroke="currentColor"
                  stroke-width="4"
                />
                <path
                  class="opacity-75"
                  fill="currentColor"
                  d="M4 12a8 8 0 018-8V0C5.373 0 0 5.373 0 12h4zm2 5.291A7.962 7.962 0 014 12H0c0 3.042 1.135 5.824 3 7.938l3-2.647z"
                />
              </svg>
              <span class="tracking-wide uppercase text-xs font-medium py-1"
                >Loading</span
              >
            </div>
          {:then}
            {#if !userObject}
              <section
                class="flex mt-8 flex-col max-w-2xl mx-auto overflow-hidden bg-white rounded-lg shadow-lg dark:bg-gray-800 md:flex-row md:h-48"
              >
                <div
                  class="md:flex md:items-center md:justify-center md:w-1/2 md:bg-gray-700 md:dark:bg-gray-800"
                >
                  <div class="px-6 py-6 md:px-8 md:py-0">
                    <h2
                      class="text-lg font-bold text-gray-700 dark:text-white md:text-gray-100"
                    >
                      Sign In For <span
                        class="text-gray-600 dark:text-gray-400 md:text-gray-300"
                        >Project</span
                      >
                    </h2>

                    <p
                      class="mt-2 text-sm text-gray-600 dark:text-gray-400 md:text-gray-400"
                    >
                      Lorem ipsum dolor sit, amet consectetur adipisicing elit.
                      Consectetur obcaecati odio
                    </p>
                  </div>
                </div>

                <div
                  class="flex items-center justify-center pb-6 md:py-0 md:w-1/2"
                >
                  <form action="#" class="py-8" autoComplete="off">
                    <div class="flex flex-col mb-2">
                      <div class="flex relative ">
                        <input
                          type="text"
                          id="sign-in-username"
                          class="flex-1 appearance-none border border-gray-300 w-full py-2 px-4 bg-white text-gray-700 placeholder-gray-400 shadow-sm text-base focus:outline-none focus:ring-2 focus:ring-purple-600 focus:border-transparent"
                          placeholder="Username"
                          bind:value={username}
                        />
                      </div>
                    </div>
                    <div class="flex flex-col mb-4">
                      <div class="flex relative ">
                        <input
                          type="password"
                          id="sign-in-email"
                          class="flex-1 appearance-none border border-gray-300 w-full py-2 px-4 bg-white text-gray-700 placeholder-gray-400 shadow-sm text-base focus:outline-none focus:ring-2 focus:ring-purple-600 focus:border-transparent"
                          placeholder="Password"
                          bind:value={password}
                        />
                      </div>
                    </div>
                    <div class="grid grid-cols-2 gap-4">
                      <div class="col-span-2 md:col-span-1 flex w-full">
                        <button
                          on:click={signIn}
                          type="button"
                          class="py-2 px-4 uppercase bg-gray-600 hover:bg-gray-700 focus:ring-gray-500 focus:ring-offset-gray-200 text-white w-full transition ease-in duration-200 text-center text-xs font-normal tracking-wider shadow-md focus:outline-none focus:ring-2 focus:ring-offset-2 "
                        >
                          Login
                        </button>
                      </div>
                      <div class="col-span-2 md:col-span-1 flex w-full">
                        <button
                          on:click={signUp}
                          type="button"
                          class="py-2 px-4 uppercase bg-gray-600 hover:bg-gray-700 focus:ring-gray-500 focus:ring-offset-gray-200 text-white w-full transition ease-in duration-200 text-center text-xs font-normal tracking-wider shadow-md focus:outline-none focus:ring-2 focus:ring-offset-2 "
                        >
                          Sign Up
                        </button>
                      </div>
                    </div>
                  </form>
                </div>
              </section>
            {:else}
              <section
                class="flex mt-0 flex-col max-w-4xl mx-auto overflow-hidden bg-transparant md:flex-row md:h-96"
              >
                <div
                  class="md:flex md:items-center md:justify-center w-full bg-transparant"
                >
                  <div class="px-0 py-6 md:px-8 md:py-0">
                    <div class="flex justify-between py-2 px-0 w-full">
                      <h2 class="text-lg font-bold text-gray-900">
                        Hi, <span class="text-gray-600 uppercase tracking-wide"
                          >{userObject.username}!</span
                        >
                      </h2>
                      <button
                        on:click={signOut}
                        type="button"
                        class="py-2 px-4 uppercase bg-gray-600 hover:bg-gray-700 focus:ring-gray-500 focus:ring-offset-gray-200 text-white w-auto transition ease-in duration-200 text-center text-sm font-semibold shadow-md focus:outline-none focus:ring-2 focus:ring-offset-2 rounded-lg "
                      >
                        Sign Out
                      </button>
                    </div>

                    {#await todoPromise}
                      <div class="bg-transparant w-full h-auto py-2 flex">
                        <svg
                          class="animate-spin -ml-1 mr-3 h-5 w-5 text-white"
                          xmlns="http://www.w3.org/2000/svg"
                          fill="none"
                          viewBox="0 0 24 24"
                        >
                          <circle
                            class="opacity-25"
                            cx="12"
                            cy="12"
                            r="10"
                            stroke="currentColor"
                            stroke-width="4"
                          />
                          <path
                            class="opacity-75"
                            fill="currentColor"
                            d="M4 12a8 8 0 018-8V0C5.373 0 0 5.373 0 12h4zm2 5.291A7.962 7.962 0 014 12H0c0 3.042 1.135 5.824 3 7.938l3-2.647z"
                          />
                        </svg>
                        <span
                          class="tracking-wide uppercase text-xs font-medium py-1 text-white"
                          >Loading</span
                        >
                      </div>
                    {:then}
                      <div>
                        <div
                          class="mt-4 container flex flex-col mx-auto w-full items-center justify-start "
                        >
                          <ul
                            class="flex flex-col mt-2 overflow-y-auto h-48 bg-transparant scrollbar scrollbar-thumb-gray-900 scrollbar-track-gray-100"
                          >
                            {#each todos as { item, itemId }, index}
                              <li class="border-white flex flex-row mb-2">
                                <div
                                  class="transition duration-500 shadow ease-in-out transform hover:-translate-y-1 hover:shadow-lg bg-gray-700 rounded-md flex flex-1 items-center p-4"
                                >
                                  <div class="flex-1 pl-1 md:mr-16">
                                    <div class="font-medium text-white">
                                      <input
                                        type="text"
                                        class="bg-gray-700"
                                        bind:value={todos[index].item}
                                        on:blur={() => updateTodo(index)}
                                      />
                                    </div>
                                  </div>
                                  <div class="text-white text-xs">TODO</div>
                                  <button
                                    type="button"
                                    on:click={() => deleteTodo(itemId)}
                                    class="w-24 text-right flex justify-end"
                                  >
                                    <svg
                                      class="w-6 h-6 text-white"
                                      fill="none"
                                      stroke="currentColor"
                                      viewBox="0 0 24 24"
                                      xmlns="http://www.w3.org/2000/svg"
                                      ><path
                                        stroke-linecap="round"
                                        stroke-linejoin="round"
                                        stroke-width="2"
                                        d="M6 18L18 6M6 6l12 12"
                                      /></svg
                                    >
                                  </button>
                                </div>
                              </li>
                            {/each}
                          </ul>
                        </div>
                      </div>

                      <div class="w-full py-4">
                        <div
                          class="flex items-center justify-center pb-0 md:py-0 w-full"
                        >
                          <form>
                            <div
                              class="flex flex-col overflow-hidden border rounded-lg dark:border-gray-600 lg:flex-row w-full"
                            >
                              <input
                                class="px-4 py-3 text-gray-700 placeholder-gray-500 bg-white outline-none dark:bg-gray-800 dark:placeholder-gray-400 focus:placeholder-transparent dark:focus:placeholder-transparent"
                                type="text"
                                name="todo"
                                placeholder="Enter todo"
                                aria-label="Enter todo"
                                bind:value={newTodo}
                              />

                              <button
                                on:click={addTodo}
                                class="px-4 py-3 text-xs font-medium tracking-wider text-gray-100 uppercase transition-colors duration-200 transform bg-gray-700 hover:bg-gray-600 focus:bg-gray-600 focus:outline-none"
                                >Add</button
                              >
                            </div>
                          </form>
                        </div>
                      </div>
                    {:catch error}
                      Error {error}
                    {/await}
                  </div>
                </div>
              </section>
            {/if}
          {:catch error}Error! {error}
          {/await}
        </div>
      </div>
    </div>
  </section>
  <footer
    class="absolute bottom-0 inset-x-0 font-sans bg-white border-t border-gray-700 text-white h-20px-4"
  >
    <div
      class="mx-auto w-full overflow-hidden flex justify-between items-center py-3 px-12"
    >
      <ul class="text-sm text-gray-700 list-none p-0 flex items-center">
        <li>
          <a href="/" class="block mr-4 w-32">
            <img src="/logo.svg" class="h-10" alt="logo" />
          </a>
        </li>
      </ul>
      <p class="inline-block py-2 px-3 text-gray-700 text-xs">
        2021 <span class="font-bold">Ikziriv Art</span>. All rights reserved.
      </p>
    </div>
  </footer>
</main>
