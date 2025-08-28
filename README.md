// Subject management section ko update karein
async function loadSubjects() {
    if (!isAuthReady || !userId) {
        console.log("Auth not ready, skipping subject load");
        return;
    }
    
    const subjectsDocRef = doc(db, `artifacts/${appId}/users/${userId}/appData/subjects`);
    try {
        const docSnap = await getDoc(subjectsDocRef);
        let subjects = [];
        
        if (docSnap.exists()) {
            const data = docSnap.data();
            // Yeh line change karein - directly data.list access karein
            subjects = Array.isArray(data.list) ? data.list : ["Maths", "Science", "English", "History"];
        } else {
            // Default subjects agar document exist nahi karta
            subjects = ["Maths", "Science", "English", "History"];
            await saveSubjects(subjects); // Create document with default subjects
        }
        
        // Populate both dropdowns
        [subjectSelect, editSubject].forEach(selectEl => {
            const currentVal = selectEl.value;
            selectEl.innerHTML = '<option value="" disabled selected>Select a Subject</option>';
            
            subjects.forEach(subject => {
                const option = document.createElement('option');
                option.value = subject;
                option.textContent = subject;
                selectEl.appendChild(option);
            });
            
            // Preserve current value if possible
            if (subjects.includes(currentVal)) {
                selectEl.value = currentVal;
            }
        });
        
    } catch (e) {
        console.error("Error loading subjects: ", e);
        // Fallback to default subjects
        const fallbackSubjects = ["Maths", "Science", "English", "History"];
        [subjectSelect, editSubject].forEach(selectEl => {
            selectEl.innerHTML = '<option value="" disabled selected>Select a Subject</option>';
            fallbackSubjects.forEach(subject => {
                const option = document.createElement('option');
                option.value = subject;
                option.textContent = subject;
                selectEl.appendChild(option);
            });
        });
    }
}

// Auth state changed handler mein loadSubjects() call karein
onAuthStateChanged(auth, async (user) => {
    if (user) {
        userId = user.uid;
        isAuthReady = true;
        setupRealtimeListener();
        await loadSubjects(); // Yeh line add karein
    } else {
        userId = null;
        isAuthReady = false;
        try {
            if (initialAuthToken) {
                await signInWithCustomToken(auth, initialAuthToken);
            } else {
                await signInAnonymously(auth);
            }
        } catch (error) {
            console.error("Authentication error:", error);
        }
    }
});

// Add subject button handler mein bhi improvement karein
addSubjectBtn.addEventListener('click', async () => {
    if (!isAuthReady || !userId) {
        subjectError.textContent = "Please wait, system initializing...";
        subjectError.classList.remove('hidden');
        return;
    }
    
    const newSubject = newSubjectInput.value.trim();
    if (!newSubject) {
        subjectError.textContent = "Please enter a subject name.";
        subjectError.classList.remove('hidden');
        return;
    }

    try {
        const subjectsDocRef = doc(db, `artifacts/${appId}/users/${userId}/appData/subjects`);
        const docSnap = await getDoc(subjectsDocRef);
        
        let subjects = [];
        if (docSnap.exists()) {
            subjects = docSnap.data().list || [];
        }
        
        // Check if subject already exists (case insensitive)
        const subjectExists = subjects.some(
            s => s.toLowerCase() === newSubject.toLowerCase()
        );
        
        if (subjectExists) {
            subjectError.textContent = "This subject already exists.";
            subjectError.classList.remove('hidden');
            return;
        }
        
        // Add new subject
        subjects.push(newSubject);
        await saveSubjects(subjects);
        await loadSubjects();
        
        newSubjectInput.value = '';
        subjectError.classList.add('hidden');
        
    } catch (error) {
        console.error("Error adding subject:", error);
        subjectError.textContent = "Could not add subject. Please try again.";
        subjectError.classList.remove('hidden');
    }
});
