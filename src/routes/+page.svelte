<script>
	// @ts-nocheck

	import { initializeApp } from 'firebase/app';
	import Editor from './editor.svelte';
	import HandleUser from './handleUsers.svelte';
	import { getFirestore } from 'firebase/firestore';
	import {
		collection,
		query,
		setDoc,
		where,
		getDocs,
		onSnapshot,
		doc,
		updateDoc,
		deleteDoc,
		addDoc
	} from 'firebase/firestore';
	import {
		getAuth,
		signInWithEmailAndPassword,
		createUserWithEmailAndPassword,
		signOut,
		onAuthStateChanged,
		signInWithPopup,
		GoogleAuthProvider,
		GithubAuthProvider
	} from 'firebase/auth';
	import { onMount } from 'svelte';
	import HandleUsers from './handleUsers.svelte';
	import { bind } from 'svelte/internal';

	let email = '';
	let password = '';
	let user = '';
	let quillEditor;

	const firebaseConfig = {
		apiKey: 'AIzaSyApAJewyPTQZmA3A83fmK2NrdkyB48PYWA',
		authDomain: 'svelte-todo-3ff8e.firebaseapp.com',
		projectId: 'svelte-todo-3ff8e',
		storageBucket: 'svelte-todo-3ff8e.appspot.com',
		messagingSenderId: '48913790170',
		appId: '1:48913790170:web:c3d5d29823f804b6aa21e3',
		measurementId: 'G-XFML1S3ZSE'
	};

	const app = initializeApp(firebaseConfig);

	let username;

	const login = () => {
		const auth = getAuth(app);
		signInWithEmailAndPassword(auth, email, password).catch((error) => {
			const errorCode = error.code;
			const errorMessage = error.message;
			console.log(errorCode, errorMessage);
		});
	};

	const signUp = () => {
		const auth = getAuth(app);
		createUserWithEmailAndPassword(auth, email, password).catch(function (error) {
			// Handle Errors here.
			var errorCode = error.code;
			var errorMessage = error.message;
			console.log(errorCode);
			console.log(errorMessage);
		});
		alert('success');
	};

	const loginWithGoogle = () => {
		const auth = getAuth(app);
		signInWithPopup(auth, new GoogleAuthProvider());
		fetchDocs();
	};

	export const logout = async () => {
		const auth = getAuth(app);
		titleArr = [];
		signOut(auth);
	};

	onMount(async () => {
		const auth = getAuth(app);
		onAuthStateChanged(auth, (newUser) => {
			console.log(user);
			user = newUser;
			fetchDocs();
		});
	});
	let db = getFirestore();
	let titleArr = [];

	export const fetchDocs = async () => {
		const auth = getAuth(app);
		username = auth.currentUser.email;
		const q = query(collection(db, 'userDocs'), where('username', '==', auth.currentUser.email));
		const querySnapshot = await getDocs(q);
		querySnapshot.forEach((doc) => {
			titleArr.push(doc.data().title);
		});
		titleArr = titleArr;
		console.log(titleArr);
	};

	const loadDocContent = async (docTitle) => {
		const auth = getAuth(app);
		let title = docTitle + '' + auth.currentUser.email;

		let content;
		console.log(title);
		let que = query(collection(db, 'userDocs'), where('username', '==', auth.currentUser.email));

		const querySnapshot = await getDocs(que);
		querySnapshot.forEach((doc) => {
			// doc.data() is never undefined for query doc snapshots
			if (doc.id == title) {
				content = doc.data().content;
				quillEditor.updateTitle(doc.data().title);
			}
		});
		quillEditor.setEditorText(content);
	};
</script>

{#if user}
	<body class="editorBody">
		<ul>
			<div class="username">
				<li style="float:right "><a style="color: black;">{username}</a></li>
			</div>

			<li style="float:right" on:click={logout}><a class="logout">Logout</a></li>
		</ul>

		<div class="row">
			<div class="column right">
				<!-- Editor prop -->
				<Editor bind:this={quillEditor} />
				<HandleUsers />
			</div>
			<div class="column docDiv">
				<h1 style="color: white;">Saved Docs</h1>

				{#each titleArr as title}
					<div class="docContainer" on:click={loadDocContent(title)}>{title}</div>
					<br />
				{/each}
			</div>
		</div>
	</body>

	<!-- LOGIN PAGE -->
{:else}
	<body class="align loginBody">
		<div class="grid">
			<h1 style="font-size: 40px; color:white">Svelte-Notepad</h1>

			<form class="form login">
				<div class="form__field">
					<label for="login__username"
						><svg class="icon">
							<use xlink:href="#icon-user" />
						</svg><span class="hidden">Username</span></label
					>
					<input
						bind:value={email}
						autocomplete="username"
						id="login__username"
						type="text"
						name="username"
						class="form__input"
						placeholder="Username"
						required
					/>
				</div>

				<div class="form__field">
					<label for="login__password"
						><svg class="icon">
							<use xlink:href="#icon-lock" />
						</svg><span class="hidden">Password</span></label
					>
					<input
						bind:value={password}
						id="login__password"
						type="password"
						name="password"
						class="form__input"
						placeholder="Password"
						required
					/>
				</div>

				<div class="form__field">
					<input on:click={login} type="submit" value="Sign In" />
				</div>

				<div class="form__field">
					<input on:click={signUp} type="submit" value="Sign up" />
				</div>

				<div class="form__field">
					<img src="src\routes\googleIcon.png" />
					<input
						on:click={loginWithGoogle}
						type="submit"
						value="Login with google"
						style="background-color: white; color:black"
					/>
				</div>
			</form>
		</div>

		<svg xmlns="http://www.w3.org/2000/svg" class="icons">
			<symbol id="icon-arrow-right" viewBox="0 0 1792 1792">
				<path
					d="M1600 960q0 54-37 91l-651 651q-39 37-91 37-51 0-90-37l-75-75q-38-38-38-91t38-91l293-293H245q-52 0-84.5-37.5T128 1024V896q0-53 32.5-90.5T245 768h704L656 474q-38-36-38-90t38-90l75-75q38-38 90-38 53 0 91 38l651 651q37 35 37 90z"
				/>
			</symbol>
			<symbol id="icon-lock" viewBox="0 0 1792 1792">
				<path
					d="M640 768h512V576q0-106-75-181t-181-75-181 75-75 181v192zm832 96v576q0 40-28 68t-68 28H416q-40 0-68-28t-28-68V864q0-40 28-68t68-28h32V576q0-184 132-316t316-132 316 132 132 316v192h32q40 0 68 28t28 68z"
				/>
			</symbol>
			<symbol id="icon-user" viewBox="0 0 1792 1792">
				<path
					d="M1600 1405q0 120-73 189.5t-194 69.5H459q-121 0-194-69.5T192 1405q0-53 3.5-103.5t14-109T236 1084t43-97.5 62-81 85.5-53.5T538 832q9 0 42 21.5t74.5 48 108 48T896 971t133.5-21.5 108-48 74.5-48 42-21.5q61 0 111.5 20t85.5 53.5 62 81 43 97.5 26.5 108.5 14 109 3.5 103.5zm-320-893q0 159-112.5 271.5T896 896 624.5 783.5 512 512t112.5-271.5T896 128t271.5 112.5T1280 512z"
				/>
			</symbol>
		</svg>
	</body>

	<!-- 
		<input type="email" id="email" placeholder="email" bind:value={email} />
		<input type="password" id="password" placeholder="password" bind:value={password} />
		<button on:click={login}>Login</button>
		<button on:click={signUp}>Sign up</button>
		<button on:click={loginWithGoogle}>Login with Google</button> -->
{/if}

<link rel="stylesheet" href="https://www.w3schools.com/w3css/4/w3.css" />

<style>
	.username {
		display: flex;
		padding: 0.6em 1.7em;
		border: 3px solid #ffffff;
		margin: 2px;
		margin-left: 6px;
		border-radius: 10px;
		box-sizing: border-box;
		text-decoration: none;
		font-family: 'Roboto', sans-serif;
		font-weight: 300;
		color: black;
		text-align: center;
		position: relative;
		justify-content: center;
		align-items: center;
		height: 40px;
		width: auto;
		float: right;
	}

	.logout {
		background-color: #0066ff;
		color: #ffffff;
		font-size: 1.3em;
		font-weight: 300;
		position: relative;
		outline: none;
		border-radius: 10px;
		display: flex;
		justify-content: center;
		align-items: center;
		cursor: pointer;
		height: 40px;
		width: 120px;
	}

	.logout:hover {
		color: #000000;
		background-color: #ffffff;
		text-decoration: none;
	}

	ul {
		list-style-type: none;
		margin: 5px;
		padding: 20px;
		overflow: hidden;
		background-color: #d9d9d9;
	}

	li {
		float: left;
	}

	li a {
		display: block;
		color: white;
		text-align: center;
		padding: 14px 16px;
		text-decoration: none;
	}

	/* Change the link color to #111 (black) on hover */

	.docContainer {
		display: inline-block;
		padding: 0.6em 1.7em;
		border: 0.1em solid #ffffff;
		margin: 5px;
		border-radius: 0.12em;
		box-sizing: border-box;
		text-decoration: none;
		font-family: 'Roboto', sans-serif;
		font-weight: 300;
		color: #ffffff;
		text-align: center;
		transition: all 0.2s;
	}

	.docContainer:hover {
		color: #000000;
		background-color: #ffffff;
	}
	.column {
		float: left;
		padding: 10px;
		height: 300px; /* Should be removed. Only for demonstration */
	}

	.docDiv {
		width: 22%;
		overflow: auto;
		background-color: #0066ff;
		border-radius: 20px;
		border: 2px #0066ff;
		margin: 8px;
	}

	.right {
		width: 75%;
	}

	/* config.css */

	:root {
		--baseColor: #606468;
	}

	/* helpers/align.css */

	.align {
		display: grid;
		place-items: center;
	}

	.loginBody {
		background-color: var(--bodyBackgroundColor);
		color: var(--bodyColor);
		font-family: var(--bodyFontFamily), var(--bodyFontFamilyFallback);
		font-size: var(--bodyFontSize);
		font-weight: var(--bodyFontWeight);
		line-height: var(--bodyLineHeight);
		height: 100vh;
		width: 100vw;
		margin: 0px;
		overflow: hidden;
	}

	.editorBody {
		background-color: white;
		color: var(--bodyColor);
		font-family: var(--bodyFontFamily), var(--bodyFontFamilyFallback);
		font-size: var(--bodyFontSize);
		font-weight: var(--bodyFontWeight);
		line-height: var(--bodyLineHeight);
		height: 100vh;
		width: 100vw;
		margin: 0px;
		overflow: hidden;
	}

	.grid {
		inline-size: 100%;
		margin-inline: auto;
		max-inline-size: 20rem;
		overflow: hidden;
	}

	/* helpers/hidden.css */

	.hidden {
		border: 0;
		clip: rect(0 0 0 0);
		height: 1px;
		margin: -1px;
		overflow: hidden;
		padding: 0;
		position: absolute;
		width: 1px;
	}

	/* helpers/icon.css */

	:root {
		--iconFill: var(--baseColor);
	}

	.icons {
		display: none;
	}

	.icon {
		block-size: 1em;
		display: inline-block;
		fill: var(--iconFill);
		inline-size: 1em;
		vertical-align: middle;
	}

	/* layout/base.css */

	:root {
		--htmlFontSize: 100%;

		--bodyBackgroundColor: #2c3338;
		--bodyColor: var(--baseColor);
		--bodyFontFamily: 'Open Sans';
		--bodyFontFamilyFallback: sans-serif;
		--bodyFontSize: 0.875rem;
		--bodyFontWeight: 400;
		--bodyLineHeight: 1.5;
	}

	* {
		box-sizing: inherit;
	}

	html {
		box-sizing: border-box;
		font-size: var(--htmlFontSize);
	}

	body {
		background-color: var(--bodyBackgroundColor);
		color: var(--bodyColor);
		font-family: var(--bodyFontFamily), var(--bodyFontFamilyFallback);
		font-size: var(--bodyFontSize);
		font-weight: var(--bodyFontWeight);
		line-height: var(--bodyLineHeight);
		margin: 0;
		min-block-size: 100vh;
	}

	/* modules/anchor.css */

	:root {
		--anchorColor: #eee;
	}

	a {
		color: var(--anchorColor);
		outline: 0;
		text-decoration: none;
	}

	a:focus,
	a:hover {
		text-decoration: underline;
	}

	/* modules/form.css */

	:root {
		--formGap: 0.875rem;
	}

	input {
		background-image: none;
		border: 0;
		color: inherit;
		font: inherit;
		margin: 0;
		outline: 0;
		padding: 0;
		transition: background-color 0.3s;
	}

	input[type='submit'] {
		cursor: pointer;
	}

	.form {
		display: grid;
		gap: var(--formGap);
	}

	.form input[type='password'],
	.form input[type='text'],
	.form input[type='submit'] {
		inline-size: 100%;
	}

	.form__field {
		display: flex;
	}

	.form__input {
		flex: 1;
	}

	/* modules/login.css */

	:root {
		--loginBorderRadus: 0.25rem;
		--loginColor: #eee;

		--loginInputBackgroundColor: #3b4148;
		--loginInputHoverBackgroundColor: #434a52;

		--loginLabelBackgroundColor: #363b41;

		--loginSubmitBackgroundColor: #ea4c88;
		--loginSubmitColor: #eee;
		--loginSubmitHoverBackgroundColor: #d44179;
	}

	.login {
		color: var(--loginColor);
	}

	.login label,
	.login input[type='text'],
	.login input[type='password'],
	.login input[type='submit'] {
		border-radius: var(--loginBorderRadus);
		padding: 1rem;
	}

	.login label {
		background-color: var(--loginLabelBackgroundColor);
		border-bottom-right-radius: 0;
		border-top-right-radius: 0;
		padding-inline: 1.25rem;
	}

	.login input[type='password'],
	.login input[type='text'] {
		background-color: var(--loginInputBackgroundColor);
		border-bottom-left-radius: 0;
		border-top-left-radius: 0;
	}

	.login input[type='password']:focus,
	.login input[type='password']:hover,
	.login input[type='text']:focus,
	.login input[type='text']:hover {
		background-color: var(--loginInputHoverBackgroundColor);
	}

	.login input[type='submit'] {
		background-color: var(--loginSubmitBackgroundColor);
		color: var(--loginSubmitColor);
		font-weight: 700;
		text-transform: uppercase;
	}

	.login input[type='submit']:focus,
	.login input[type='submit']:hover {
		background-color: var(--loginSubmitHoverBackgroundColor);
	}

	/* modules/text.css */

	p {
		margin-block: 1.5rem;
	}

	.text--center {
		text-align: center;
	}
</style>
