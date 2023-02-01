<script lang="ts">
	import { dev } from "$app/environment"
	import firebaseConfig from "$lib/firebaseConfig.json"
	import { type FirebaseApp, initializeApp } from "firebase/app"
	import {
		type Auth,
		GithubAuthProvider,
		browserSessionPersistence,
		connectAuthEmulator,
		getAuth,
		setPersistence,
		signInWithRedirect,
	} from "firebase/auth"
	import { Button, P } from "flowbite-svelte"
	import { onMount } from "svelte"

	enum AuthState {
		uninitialized,
		signedIn,
		notSignedIn,
	}

	let app: FirebaseApp
	let auth: Auth
	let authState: AuthState = AuthState.uninitialized

	// fallback if auth state check hangs
	setTimeout(() => {
		if (authState == AuthState.uninitialized) authState = AuthState.notSignedIn
	}, 1000)

	async function login() {
		const provider = new GithubAuthProvider()
		// https://docs.github.com/en/developers/apps/building-oauth-apps/scopes-for-oauth-apps#available-scopes
		provider.addScope("user:email")

		await signInWithRedirect(auth, provider)
	}

	onMount(async () => {
		app = initializeApp(firebaseConfig)
		auth = getAuth(app)
		auth.onAuthStateChanged(() => {
			authState = auth.currentUser ? AuthState.signedIn : AuthState.notSignedIn
		})

		if (dev) connectAuthEmulator(auth, "http://localhost:9099")

		setPersistence(auth, browserSessionPersistence)
	})
</script>

<head>
	<title>GDSC Open Community</title>
</head>

<div class="flex flex-col gap-3">
	{#if authState == AuthState.uninitialized}
		<P size="2xl">가입 상태 확인 중</P>
	{:else if authState == AuthState.signedIn}
		<P size="2xl">가입 완료. 창을 닫으셔도 좋습니다.</P>
		<Button on:click={login}>다른 GitHub 계정으로 가입</Button>
	{:else if authState == AuthState.notSignedIn}
		<Button on:click={login}>GitHub 계정으로 등록</Button>
	{/if}
</div>
