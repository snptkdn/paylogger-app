<script lang="ts">
  const fetchTodayAmount = async() => {
    const today = new Date();
    const res = await getAmountOfRange(today.getFullYear(), today.getMonth()+1, today.getDate());

    if (res) {
      return res.json();
    } else {
      throw new Error('Error');
    }
  }

  const fetchThisMonthAmount = async() => {
    const today = new Date();
    const res = await getAmountOfRange(today.getFullYear(), today.getMonth()+1, undefined);

    if (res) {
      return res.json();
    } else {
      throw new Error('Error');
    }
  }

  const fetchTotalAmount = async() => {
    const res = await getAmountOfRange(undefined, undefined, undefined);

    if (res) {
      return res.json();
    } else {
      throw new Error('Error');
    }
  }

  const fetchCategories = async() => {
    const res = await getCategories();

    if (res) {
      return res.json();
    } else {
      throw new Error("Error");
    }
  }

  async function getAmountOfRange(year?: number, month?: number, day?: number): Promise<Response> {
    const yearQuery = year === undefined? "" : `year=${year}&`;
    const monthQuery = month === undefined ? "" : `month=${month}&`;
    const dayQuery = day === undefined ? "" : `day=${day}`;

    const res = await fetch(
      `http://34.127.13.199:8000/log/total?${yearQuery}${monthQuery}${dayQuery}`
    )

    return res;
  }

  async function getCategories(): Promise<Response> {
    return await fetch(
      "http://34.127.13.199:8000/category"
    );
  }


  let today_amount = fetchTodayAmount();
  let thismonth_amount = fetchThisMonthAmount();
  let total_amount = fetchTotalAmount();
  let categories = fetchCategories();
  let sel:BigInt;
</script>

<main>
	<h1>Welcome to PayLogger!</h1>
  <h2>
    {#await total_amount}
      loading...
    {:then string}
      You have spent <em>¥{string}</em> so far since you started using this application !!
    {:catch}
      Sorry, can not get today's amount...
      Maybe Server is down...
    {/await}
  </h2>
  <h2>
    {#await today_amount then string}
      TODAY:¥{string}
    {/await}
  </h2>
  <h2>
    {#await thismonth_amount then string}
      THIS MONTH:¥{string}
    {/await}
  </h2>
  <h2>
    {#await categories then categories}
     <select bind:value={sel}>
      <option value="">Category</option>
      {#each categories as category}
        <option value={category.id} >{category.name}</option>
      {/each}
      </select>
    {/await}
  </h2>

</main>



<style>
	main {
		text-align: center;
		padding: 1em;
		max-width: 240px;
		margin: 0 auto;
	}

	h1 {
		color: #ff3e00;
		font-size: 4em;
		font-weight: 100;
	}

	@media (min-width: 640px) {
		main {
			max-width: none;
		}
	}
</style>
