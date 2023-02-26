<script lang="ts">
	import { dev } from "$app/environment"
	import firebaseConfig from "$lib/firebaseConfig.json"
	import { toast } from "@zerodevx/svelte-toast"
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

	async function login() {
		const provider = new GithubAuthProvider()
		// https://docs.github.com/en/developers/apps/building-oauth-apps/scopes-for-oauth-apps#available-scopes
		provider.addScope("user:email")

		await signInWithRedirect(auth, provider)
	}

	/**
	 * AuthState가 signedIn일 시에만 작동
	 */
	async function copyUIDToClipboard() {
		await navigator.clipboard.writeText(auth.currentUser?.uid || "")
		toast.push("복사됨")
	}

	onMount(async () => {
		app = initializeApp(firebaseConfig)
		auth = getAuth(app)
		auth.onAuthStateChanged(() => {
			authState = auth.currentUser ? AuthState.signedIn : AuthState.notSignedIn
		})

		if (dev) connectAuthEmulator(auth, "http://localhost:9099")

		await setPersistence(auth, browserSessionPersistence)
	})
</script>

<svelte:head>
	<title>GDSC Open Community</title>
</svelte:head>

<div class="m-5 flex flex-col gap-3">
	{#if authState == AuthState.uninitialized}
		<Button on:click={login}>GitHub 계정으로 등록</Button>
	{:else if authState == AuthState.notSignedIn}
		<Button on:click={login}>GitHub 계정으로 등록</Button>
	{:else if authState == AuthState.signedIn}
		<P size="2xl">
			가입 완료. 디스코드 서버에서 "/등록" 명령어를 이용해 본인 인증 코드를
			등록하세요.
		</P>
		<Button on:click={copyUIDToClipboard}>코드 복사</Button>
		<Button on:click={login}>다른 GitHub 계정으로 가입</Button>
	{/if}
</div>
