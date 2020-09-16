<script>
	// import { onMount } from 'svelte';
	import MovieItem from './Movie/item.svelte';

	const APIKEY = '29ed1d64cc3508c30f08131eb1860d99';
	const BASEURL = `https://api.themoviedb.org/3`;
	const APISETTINGS = `?api_key=${APIKEY}&language=es`;

	// let movies = [];

	// function fetchMovies() {
	// 	const URL = ` ${BASEURL}/discover/movie${APISETTINGS}&sort_by=popularity.desc`;
	// 	fetch(URL)
	// 		.then((response) => response.json())
	// 		.then(({ results }) => {
	// 			movies = results;
	// 			console.log(movies);
	// 		});
	// }

	const movies = (async () => {
		const URL = ` ${BASEURL}/discover/movie${APISETTINGS}&sort_by=popularity.desc`;
		const response = await fetch(URL);
		return await response.json();
	})();

	let likedMovies = [];

	function toggleLike(event) {
		// console.log(event);
		const movie = event.detail;

		let index = likedMovies.findIndex((m) => m.id === movie.id);
		if (index >= 0) {
			likedMovies.splice(index, 1);
		} else {
			likedMovies.push(movie);
		}
		likedMovies = likedMovies;
	}

	$: like = (id) => {
		let index = likedMovies.findIndex((m) => m.id === id);
		return index >= 0;
	};
	// onMount(() => {
	// 	console.log('the component has mounted');
	// 	fetchMovies();
	// });
</script>

<style>
	main {
		text-align: center;
		max-width: 240px;
	}
	h2 {
		margin-top: 5%;
		color: white;
	}
	.titlePrincipal {
		text-transform: uppercase;
		border: 2px solid white;
	}
	.msg {
		color: white;
	}
	.panel {
		height: 100vh;
		overflow: auto;
	}

	@media (min-width: 640px) {
		main {
			max-width: none;
		}
	}
</style>

<svelte:head>
	<title>Movies Svelte</title>
	<link
		rel="stylesheet"
		href="https://stackpath.bootstrapcdn.com/bootstrap/4.5.2/css/bootstrap.min.css"
		integrity="sha384-JcKb8q3iqJ61gNV9KGb8thSsNjpSL0n8PARn9HuZOnIxN0hoP+VmmDGMN5t9UJ0Z"
		crossorigin="anonymous" />
</svelte:head>
<main class="container">
	<div class="row">
		<div class="col-12 col-md-6 col-lg-8 panel">
			<h2 class="titlePrincipal">Películas</h2>
			<!-- {#each movies as movie}
			<div class="col-12 col-md-6 col-lg-3 p-2">
				<MovieItem
					id={movie.id}
					title={movie.title}
					overview={movie.overview}
					cover={movie.poster_path} />
			</div>
		{/each} -->
			<div class="row">
				{#await movies}
					<!-- promise is pending -->
					<div class="col 12">
						<p>Cargando datos...</p>
					</div>
				{:then data}
					<!-- promise was fulfilled -->
					{#each data.results as movie}
						<div class="col-12 col-md-6 col-lg-4 p-2">
							<MovieItem
								like={like(movie.id)}
								id={movie.id}
								title={movie.title}
								overview={movie.overview}
								cover={movie.poster_path}
								on:onToggleLike={toggleLike} />
						</div>
					{/each}
				{/await}
			</div>
		</div>
		<div class="col-12 col-md-6 col-lg-4 p-2">
			<h2>Películas favoritas</h2>
			<div class="row">
				{#if likedMovies.length}
					{#each likedMovies as movie, i (movie.id)}
						<div class="col-12 col-md-6 col-lg-4 p-2">
							<MovieItem
								like={like(movie.id)}
								id={movie.id}
								title={movie.title}
								overview=""
								cover={movie.cover}
								on:onToggleLike={toggleLike} />
						</div>
					{/each}
				{:else}
					<div class="col 12">
						<p class="msg">No tienes películas favoritas</p>
					</div>
				{/if}
				<div />
			</div>
		</div>
	</div>
</main>
