<script>
	// @ts-nocheck
	import { initializeApp } from 'firebase/app';
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
	import { getAuth } from 'firebase/auth';

	const auth = getAuth();
	let user = auth.currentUser.email;

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

	const db = getFirestore();
	console.log(db);
	const colRef = query(collection(db, 'userDocs'));

	export const addData = async (title, text) => {
		await setDoc(doc(db, 'userDocs', title + '' + user), {
			username: user,
			content: String(text),
			title: title
		});
		location.reload();
	};
</script>
