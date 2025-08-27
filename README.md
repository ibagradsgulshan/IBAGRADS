<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Student Result Uploader</title>
    <!-- Tailwind CSS CDN for styling -->
    <script src="https://cdn.tailwindcss.com"></script>
    <!-- Google Fonts: Inter -->
    <link rel="preconnect" href="https://fonts.googleapis.com">
    <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600;700&display=swap" rel="stylesheet">
    <!-- jspdf and html2canvas for PDF generation -->
    <script src="https://cdnjs.cloudflare.com/ajax/libs/html2canvas/1.4.1/html2canvas.min.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/jspdf/2.5.1/jspdf.umd.min.js"></script>
    <style>
        /* Using Inter font as the default */
        body {
            font-family: 'Inter', sans-serif;
            background-color: #f1f5f9; /* Fallback color */
        }
        /* Modal and Result Card Styling */
        .modal-overlay {
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background-color: rgba(0, 0, 0, 0.7);
            display: flex;
            justify-content: center;
            align-items: center;
            z-index: 1000;
            opacity: 0;
            visibility: hidden;
            transition: opacity 0.3s ease, visibility 0.3s ease;
        }
        .modal-overlay.active {
            opacity: 1;
            visibility: visible;
        }
        .modal-container {
            position: relative;
            background-color: white;
            padding: 24px;
            border-radius: 12px;
            box-shadow: 0 10px 15px -3px rgba(0, 0, 0, 0.1), 0 4px 6px -2px rgba(0, 0, 0, 0.05);
            max-height: 90vh;
            overflow-y: auto;
            transform: translateY(-20px);
            transition: transform 0.3s ease;
        }
        .modal-overlay.active .modal-container {
            transform: translateY(0);
        }
        #resultCardContent {
            background: linear-gradient(135deg, #f3f4f6, #e5e7eb);
            padding: 2.5rem;
            border-radius: 1rem;
            box-shadow: inset 0 2px 4px rgba(0,0,0,0.06);
            border: 1px solid #d1d5db;
        }
        .result-card-header {
            border-bottom: 2px solid #6b7280;
        }
        .result-card-item {
            display: flex;
            justify-content: space-between;
            align-items: center;
            padding: 0.75rem 0;
            border-bottom: 1px dashed #d1d5db;
        }
        /* NEW: Background for PDF export */
        .printable-background {
            background: linear-gradient(135deg, #e0eafc 0%, #cfdef3 100%);
        }
    </style>
</head>
<body class="bg-slate-100 flex items-center justify-center min-h-screen p-4 sm:p-6">

    <div class="w-full max-w-5xl mx-auto bg-white rounded-2xl shadow-lg p-6 sm:p-8">
        
        <!-- Header Section -->
        <div class="text-center mb-6">
            <h1 class="text-3xl sm:text-4xl font-bold text-gray-800">Student Result Manager</h1>
            <p class="text-gray-500 mt-2">Yahan student ke results add aur manage karein.</p>
        </div>
        
        <!-- Form to add new result -->
        <div class="bg-gray-50 p-6 rounded-xl border border-gray-200 mb-8">
            <h2 class="text-2xl font-semibold text-gray-700 mb-4">Naya Result Add Karein</h2>
            <form id="resultForm" class="grid grid-cols-1 md:grid-cols-3 lg:grid-cols-4 gap-4 items-end">
                <!-- Inputs are organized for better layout -->
                <div>
                    <label for="studentName" class="block text-sm font-medium text-gray-600 mb-1">Student ka Naam</label>
                    <input type="text" id="studentName" placeholder="Jaise: Anil Kumar" class="w-full px-4 py-2 bg-white border border-gray-300 rounded-lg focus:ring-2 focus:ring-blue-500 focus:border-blue-500 transition" required>
                </div>
                <div>
                    <label for="studentClass" class="block text-sm font-medium text-gray-600 mb-1">Class</label>
                    <select id="studentClass" class="w-full px-4 py-2 bg-white border border-gray-300 rounded-lg focus:ring-2 focus:ring-blue-500 focus:border-blue-500 transition" required>
                        <option value="" disabled selected>Select Class</option>
                        <option value="XI">XI</option>
                        <option value="XII">XII</option>
                    </select>
                </div>
                <div>
                    <label for="studentField" class="block text-sm font-medium text-gray-600 mb-1">Field</label>
                    <select id="studentField" class="w-full px-4 py-2 bg-white border border-gray-300 rounded-lg focus:ring-2 focus:ring-blue-500 focus:border-blue-500 transition" required>
                        <option value="" disabled selected>Select Field</option>
                        <option value="Pre med">Pre med</option>
                        <option value="Pre eng">Pre eng</option>
                        <option value="CS">CS</option>
                    </select>
                </div>
                <div>
                    <label for="subject" class="block text-sm font-medium text-gray-600 mb-1">Subject</label>
                    <select id="subject" class="w-full px-4 py-2 bg-white border border-gray-300 rounded-lg focus:ring-2 focus:ring-blue-500 focus:border-blue-500 transition" required>
                        <option value="" disabled selected>Select a Subject</option>
                    </select>
                </div>
                <div>
                    <label for="topicName" class="block text-sm font-medium text-gray-600 mb-1">Topic ka Naam</label>
                    <input type="text" id="topicName" placeholder="Jaise: Algebra" class="w-full px-4 py-2 bg-white border border-gray-300 rounded-lg focus:ring-2 focus:ring-blue-500 focus:border-blue-500 transition" required>
                </div>
                <div>
                    <label for="score" class="block text-sm font-medium text-gray-600 mb-1">Score</label>
                    <input type="number" id="score" placeholder="Jaise: 85" min="0" class="w-full px-4 py-2 bg-white border border-gray-300 rounded-lg focus:ring-2 focus:ring-blue-500 focus:border-blue-500 transition" required>
                </div>
                <div>
                    <label for="totalMarks" class="block text-sm font-medium text-gray-600 mb-1">Total Marks</label>
                    <input type="number" id="totalMarks" value="100" min="1" class="w-full px-4 py-2 bg-white border border-gray-300 rounded-lg focus:ring-2 focus:ring-blue-500 focus:border-blue-500 transition" required>
                </div>
                <div>
                    <label for="resultDate" class="block text-sm font-medium text-gray-600 mb-1">Date</label>
                    <input type="date" id="resultDate" class="w-full px-4 py-2 bg-white border border-gray-300 rounded-lg focus:ring-2 focus:ring-blue-500 focus:border-blue-500 transition" required>
                </div>
                <div class="col-span-1 md:col-span-3 lg:col-span-4">
                    <button type="submit" class="w-full bg-blue-600 text-white font-semibold py-2 px-4 rounded-lg hover:bg-blue-700 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-blue-500 transition duration-300 ease-in-out transform hover:scale-105">
                        Add Result
                    </button>
                </div>
            </form>
            <p id="errorMessage" class="text-red-500 text-sm mt-2 hidden">Please sabhi fields bharein.</p>
        </div>

        <!-- Subject Management Section -->
        <div class="bg-gray-50 p-6 rounded-xl border border-gray-200 mb-8">
            <h2 class="text-2xl font-semibold text-gray-700 mb-4">Subjects Manage Karein</h2>
            <div class="flex flex-col sm:flex-row gap-4">
                <input type="text" id="newSubjectInput" placeholder="Naya Subject Add Karein" class="flex-grow px-4 py-2 bg-white border border-gray-300 rounded-lg focus:ring-2 focus:ring-blue-500 focus:border-blue-500 transition">
                <button id="addSubjectBtn" class="bg-green-500 text-white font-semibold py-2 px-4 rounded-lg hover:bg-green-600 transition duration-300">
                    Add Subject
                </button>
                <button id="removeSubjectBtn" class="bg-red-500 text-white font-semibold py-2 px-4 rounded-lg hover:bg-red-600 transition duration-300">
                    Remove Selected Subject
                </button>
            </div>
            <p id="subjectError" class="text-red-500 text-sm mt-2 hidden"></p>
        </div>

        <!-- Export and Results Table -->
        <div class="overflow-x-auto">
            <div class="flex flex-col sm:flex-row justify-between items-center mb-4">
                <h2 class="text-2xl font-semibold text-gray-700 mb-2 sm:mb-0">Uploaded Results</h2>
                <div class="flex space-x-2">
                    <!-- Final Result Button -->
                    <button id="showFinalResultBtn" class="bg-purple-600 text-white font-semibold py-2 px-4 rounded-lg hover:bg-purple-700 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-purple-500 transition duration-300 ease-in-out transform hover:scale-105">
                        Final Results
                    </button>
                    <button id="exportPdfBtn" class="bg-red-600 text-white font-semibold py-2 px-4 rounded-lg hover:bg-red-700 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-red-500 transition duration-300 ease-in-out transform hover:scale-105">
                        PDF Export
                    </button>
                    <button id="exportExcelBtn" class="bg-green-600 text-white font-semibold py-2 px-4 rounded-lg hover:bg-green-700 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-green-500 transition duration-300 ease-in-out transform hover:scale-105">
                        Excel Export
                    </button>
                </div>
            </div>

            <div class="mb-4">
                <input type="text" id="searchInput" placeholder="Student ke naam se khojein..." class="w-full px-4 py-2 bg-white border border-gray-300 rounded-lg focus:ring-2 focus:ring-blue-500 focus:border-blue-500 transition">
            </div>

            <table id="resultsTable" class="min-w-full bg-white rounded-lg shadow overflow-hidden">
                <thead class="bg-gray-800 text-white">
                    <tr>
                        <th class="text-left py-3 px-4 uppercase font-semibold text-sm">Student Naam</th>
                        <th class="text-left py-3 px-4 uppercase font-semibold text-sm">Class</th>
                        <th class="text-left py-3 px-4 uppercase font-semibold text-sm">Field</th>
                        <th class="text-left py-3 px-4 uppercase font-semibold text-sm">Subject</th>
                        <th class="text-left py-3 px-4 uppercase font-semibold text-sm">Topic</th>
                        <th class="text-left py-3 px-4 uppercase font-semibold text-sm">Score</th>
                        <th class="text-left py-3 px-4 uppercase font-semibold text-sm">Percentage</th>
                        <th class="text-left py-3 px-4 uppercase font-semibold text-sm">Date</th>
                        <th class="text-center py-3 px-4 uppercase font-semibold text-sm">Actions</th>
                    </tr>
                </thead>
                <tbody id="resultsTableBody" class="text-gray-700">
                    <!-- Results will be dynamically inserted here -->
                </tbody>
            </table>
            <div id="loadingMessage" class="text-center py-10 text-gray-500"><p>Results load ho rahe hain...</p></div>
            <div id="noResultsMessage" class="text-center py-10 text-gray-500 hidden"><p>Abhi tak koi result upload nahi hua hai.</p></div>
        </div>
    </div>

    <!-- Modal for Result Card -->
    <div id="cardModal" class="modal-overlay">
        <div class="modal-container w-full max-w-sm sm:max-w-md">
            <div id="resultCardContent"></div>
            <div class="flex justify-end space-x-2 mt-4">
                <button id="exportCardPdfBtn" class="bg-blue-600 text-white font-semibold py-2 px-4 rounded-lg hover:bg-blue-700 transition">PDF Export</button>
                <button id="closeCardModalBtn" class="bg-gray-300 text-gray-800 font-semibold py-2 px-4 rounded-lg hover:bg-gray-400 transition">Band Karein</button>
            </div>
        </div>
    </div>

    <!-- Modal for Editing Result -->
    <div id="editModal" class="modal-overlay">
        <div class="modal-container w-full max-w-lg">
            <h2 class="text-2xl font-semibold text-gray-700 mb-4">Result Edit Karein</h2>
            <form id="editForm">
                <input type="hidden" id="editDocId">
                <div class="grid grid-cols-1 md:grid-cols-2 gap-4">
                    <div>
                        <label for="editStudentName" class="block text-sm font-medium text-gray-600 mb-1">Student ka Naam</label>
                        <input type="text" id="editStudentName" class="w-full px-4 py-2 bg-white border border-gray-300 rounded-lg" required>
                    </div>
                    <div>
                        <label for="editStudentClass" class="block text-sm font-medium text-gray-600 mb-1">Class</label>
                        <select id="editStudentClass" class="w-full px-4 py-2 bg-white border border-gray-300 rounded-lg" required>
                            <option value="XI">XI</option>
                            <option value="XII">XII</option>
                        </select>
                    </div>
                     <div>
                        <label for="editStudentField" class="block text-sm font-medium text-gray-600 mb-1">Field</label>
                        <select id="editStudentField" class="w-full px-4 py-2 bg-white border border-gray-300 rounded-lg" required>
                            <option value="Pre med">Pre med</option>
                            <option value="Pre eng">Pre eng</option>
                            <option value="CS">CS</option>
                        </select>
                    </div>
                    <div>
                        <label for="editSubject" class="block text-sm font-medium text-gray-600 mb-1">Subject</label>
                        <select id="editSubject" class="w-full px-4 py-2 bg-white border border-gray-300 rounded-lg" required></select>
                    </div>
                    <div>
                        <label for="editTopicName" class="block text-sm font-medium text-gray-600 mb-1">Topic ka Naam</label>
                        <input type="text" id="editTopicName" class="w-full px-4 py-2 bg-white border border-gray-300 rounded-lg" required>
                    </div>
                    <div>
                        <label for="editScore" class="block text-sm font-medium text-gray-600 mb-1">Score</label>
                        <input type="number" id="editScore" min="0" class="w-full px-4 py-2 bg-white border border-gray-300 rounded-lg" required>
                    </div>
                    <div>
                        <label for="editTotalMarks" class="block text-sm font-medium text-gray-600 mb-1">Total Marks</label>
                        <input type="number" id="editTotalMarks" min="1" class="w-full px-4 py-2 bg-white border border-gray-300 rounded-lg" required>
                    </div>
                    <div class="col-span-1 md:col-span-2">
                        <label for="editResultDate" class="block text-sm font-medium text-gray-600 mb-1">Date</label>
                        <input type="date" id="editResultDate" class="w-full px-4 py-2 bg-white border border-gray-300 rounded-lg" required>
                    </div>
                </div>
                <div class="flex justify-end space-x-2 mt-6">
                    <button type="submit" class="bg-blue-600 text-white font-semibold py-2 px-4 rounded-lg hover:bg-blue-700 transition">Save Changes</button>
                    <button type="button" id="closeEditModalBtn" class="bg-gray-300 text-gray-800 font-semibold py-2 px-4 rounded-lg hover:bg-gray-400 transition">Cancel</button>
                </div>
            </form>
        </div>
    </div>

    <!-- Modal for Final Results -->
    <div id="finalResultModal" class="modal-overlay">
        <div class="modal-container w-full max-w-4xl printable-background">
            <div class="flex flex-col sm:flex-row justify-between items-center mb-4">
                 <h2 class="text-2xl font-semibold text-gray-700">Final Consolidated Results</h2>
                 <!-- Filter Buttons -->
                 <div id="finalResultFilters" class="flex justify-start space-x-2">
                     <button data-filter="Pre med" class="filter-btn bg-gray-500 text-white font-semibold py-1 px-3 rounded-lg hover:bg-gray-600 transition">Pre med</button>
                     <button data-filter="Pre eng" class="filter-btn bg-gray-500 text-white font-semibold py-1 px-3 rounded-lg hover:bg-gray-600 transition">Pre eng</button>
                     <button data-filter="CS" class="filter-btn bg-gray-500 text-white font-semibold py-1 px-3 rounded-lg hover:bg-gray-600 transition">CS</button>
                     <button data-filter="All" class="filter-btn bg-blue-500 text-white font-semibold py-1 px-3 rounded-lg hover:bg-blue-600 transition">All</button>
                 </div>
            </div>
            <div class="overflow-x-auto">
                <table id="finalResultTable" class="min-w-full bg-white rounded-lg shadow overflow-hidden">
                    <thead class="bg-gray-800 text-white">
                        <tr>
                            <th class="text-left py-3 px-4 uppercase font-semibold text-sm">Student Naam</th>
                            <th class="text-left py-3 px-4 uppercase font-semibold text-sm">Class</th>
                            <th class="text-left py-3 px-4 uppercase font-semibold text-sm">Field</th>
                            <th class="text-left py-3 px-4 uppercase font-semibold text-sm">Total Score</th>
                            <th class="text-left py-3 px-4 uppercase font-semibold text-sm">Total Marks</th>
                            <th class="text-left py-3 px-4 uppercase font-semibold text-sm">Overall Percentage</th>
                            <th class="text-left py-3 px-4 uppercase font-semibold text-sm">Grade</th>
                        </tr>
                    </thead>
                    <tbody id="finalResultTableBody" class="text-gray-700">
                        <!-- Final results will be dynamically inserted here -->
                    </tbody>
                </table>
            </div>
            <div class="flex justify-end space-x-2 mt-6">
                <button id="exportFinalResultPdfBtn" class="bg-red-600 text-white font-semibold py-2 px-4 rounded-lg hover:bg-red-700 transition">Export PDF</button>
                <button type="button" id="closeFinalResultModalBtn" class="bg-gray-300 text-gray-800 font-semibold py-2 px-4 rounded-lg hover:bg-gray-400 transition">Band Karein</button>
            </div>
        </div>
    </div>


    <!-- Firebase Scripts -->
    <script type="module">
        import { initializeApp } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-app.js";
        import { getAuth, signInAnonymously, signInWithCustomToken, onAuthStateChanged } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-auth.js";
        import { getFirestore, doc, addDoc, deleteDoc, onSnapshot, collection, getDoc, setDoc, updateDoc } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-firestore.js";

        // Global variables provided by the environment. Do not change these.
        const appId = typeof __app_id !== 'undefined' ? __app_id : 'default-app-id';
        const firebaseConfig = typeof __firebase_config !== 'undefined' ? JSON.parse(__firebase_config) : {};
        const initialAuthToken = typeof __initial_auth_token !== 'undefined' ? __initial_auth_token : null;

        // --- DOM Element References ---
        const resultForm = document.getElementById('resultForm');
        const resultsTableBody = document.getElementById('resultsTableBody');
        const errorMessage = document.getElementById('errorMessage');
        const noResultsMessage = document.getElementById('noResultsMessage');
        const loadingMessage = document.getElementById('loadingMessage');
        const exportPdfBtn = document.getElementById('exportPdfBtn');
        const exportExcelBtn = document.getElementById('exportExcelBtn');
        const resultsTable = document.getElementById('resultsTable');
        const searchInput = document.getElementById('searchInput'); 
        const studentFieldSelect = document.getElementById('studentField');
        const subjectSelect = document.getElementById('subject');
        const newSubjectInput = document.getElementById('newSubjectInput');
        const addSubjectBtn = document.getElementById('addSubjectBtn');
        const removeSubjectBtn = document.getElementById('removeSubjectBtn');
        const subjectError = document.getElementById('subjectError');
        
        // Result Card Modal Elements
        const cardModal = document.getElementById('cardModal');
        const closeCardModalBtn = document.getElementById('closeCardModalBtn');
        const resultCardContent = document.getElementById('resultCardContent');
        const exportCardPdfBtn = document.getElementById('exportCardPdfBtn');

        // Edit Modal Elements
        const editModal = document.getElementById('editModal');
        const editForm = document.getElementById('editForm');
        const closeEditModalBtn = document.getElementById('closeEditModalBtn');
        const editDocId = document.getElementById('editDocId');
        const editStudentName = document.getElementById('editStudentName');
        const editStudentClass = document.getElementById('editStudentClass');
        const editStudentField = document.getElementById('editStudentField');
        const editSubject = document.getElementById('editSubject');
        const editTopicName = document.getElementById('editTopicName');
        const editScore = document.getElementById('editScore');
        const editTotalMarks = document.getElementById('editTotalMarks');
        const editResultDate = document.getElementById('editResultDate');

        // Final Result Modal Elements
        const finalResultModal = document.getElementById('finalResultModal');
        const showFinalResultBtn = document.getElementById('showFinalResultBtn');
        const closeFinalResultModalBtn = document.getElementById('closeFinalResultModalBtn');
        const finalResultTableBody = document.getElementById('finalResultTableBody');
        const exportFinalResultPdfBtn = document.getElementById('exportFinalResultPdfBtn');
        const finalResultFilters = document.getElementById('finalResultFilters');

        // --- Firebase Initialization ---
        const app = initializeApp(firebaseConfig);
        const db = getFirestore(app);
        const auth = getAuth(app);
        let userId = null;
        let isAuthReady = false;
        let currentResults = []; // Cache for current results
        let allSubjects = []; // Simplified to a single array

        // --- Utility Functions ---
        const checkTableEmpty = () => {
            noResultsMessage.classList.toggle('hidden', resultsTableBody.rows.length > 0);
        };

        const showModal = (modalElement) => {
            modalElement.classList.add('active');
        };

        const hideModal = (modalElement) => {
            modalElement.classList.remove('active');
        };

        // --- Authentication ---
        onAuthStateChanged(auth, async (user) => {
            if (user) {
                userId = user.uid;
                isAuthReady = true;
                setupRealtimeListener();
                loadSubjects();
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

        // --- Firestore Real-time Listener ---
        function setupRealtimeListener() {
            if (!isAuthReady || !userId) return;
            const resultsCollectionRef = collection(db, `artifacts/${appId}/users/${userId}/results`);

            onSnapshot(resultsCollectionRef, (snapshot) => {
                loadingMessage.classList.add('hidden');
                resultsTableBody.innerHTML = '';
                currentResults = []; 
                
                const sortedDocs = snapshot.docs.sort((a, b) => {
                    const dateA = a.data().timestamp?.toDate() || 0;
                    const dateB = b.data().timestamp?.toDate() || 0;
                    return dateB - dateA;
                });

                sortedDocs.forEach(doc => {
                    const data = doc.data();
                    const docId = doc.id;
                    currentResults.push({ id: docId, ...data });
                    
                    const newRow = document.createElement('tr');
                    newRow.dataset.studentName = data.studentName.toLowerCase();
                    newRow.classList.add('border-b', 'border-gray-200', 'hover:bg-gray-50');

                    const percentage = data.totalMarks > 0 ? ((data.score / data.totalMarks) * 100).toFixed(2) : 'N/A';
                    const resultDate = data.resultDate ? new Date(data.resultDate).toLocaleDateString() : 'N/A';

                    newRow.innerHTML = `
                        <td class="py-3 px-4">${data.studentName}</td>
                        <td class="py-3 px-4">${data.studentClass}</td>
                        <td class="py-3 px-4">${data.studentField || 'N/A'}</td>
                        <td class="py-3 px-4">${data.subject}</td>
                        <td class="py-3 px-4">${data.topicName || 'N/A'}</td> 
                        <td class="py-3 px-4">${data.score} / ${data.totalMarks}</td>
                        <td class="py-3 px-4">${percentage}%</td> 
                        <td class="py-3 px-4">${resultDate}</td>
                        <td class="py-3 px-4">
                            <div class="flex items-center justify-center space-x-2">
                                <button data-id="${docId}" class="view-card-btn bg-blue-500 text-white text-xs font-semibold py-1 px-3 rounded-full hover:bg-blue-600 transition">View</button>
                                <button data-id="${docId}" class="edit-btn bg-yellow-500 text-white text-xs font-semibold py-1 px-3 rounded-full hover:bg-yellow-600 transition">Edit</button>
                                <button data-id="${docId}" class="delete-btn bg-red-500 text-white text-xs font-semibold py-1 px-3 rounded-full hover:bg-red-600 transition">Delete</button>
                            </div>
                        </td>
                    `;
                    resultsTableBody.appendChild(newRow);
                });
                checkTableEmpty();
            }, (error) => {
                console.error("Error fetching documents: ", error);
                loadingMessage.classList.add('hidden');
                checkTableEmpty();
            });
        }

        // --- Subject Management ---
        function populateSubjectDropdowns() {
            [subjectSelect, editSubject].forEach(selectEl => {
                const currentValue = selectEl.value;
                selectEl.innerHTML = '<option value="" disabled selected>Select a Subject</option>';
                allSubjects.forEach(subject => {
                    const option = document.createElement('option');
                    option.value = subject;
                    option.textContent = subject;
                    selectEl.appendChild(option);
                });
                // Try to restore previous selection, otherwise default to placeholder
                if (allSubjects.includes(currentValue)) {
                    selectEl.value = currentValue;
                } else {
                    selectEl.selectedIndex = 0;
                }
            });
        }

        async function loadSubjects() {
            if (!isAuthReady || !userId) return;
            const subjectsDocRef = doc(db, `artifacts/${appId}/users/${userId}/appData/subjects`);
            try {
                const docSnap = await getDoc(subjectsDocRef);
                if (docSnap.exists() && docSnap.data().list) {
                    allSubjects = JSON.parse(docSnap.data().list);
                } else {
                    // Default subjects if none exist in Firestore
                    allSubjects = ["Physics", "Chemistry", "Maths", "English", "Biology", "Urdu"];
                    await saveSubjects(); 
                }
                populateSubjectDropdowns();
            } catch (e) {
                console.error("Error loading subjects: ", e);
                // Fallback to default if parsing fails or another error occurs
                allSubjects = ["Physics", "Chemistry", "Maths", "English", "Biology", "Urdu"];
                populateSubjectDropdowns();
            }
        }

        async function saveSubjects() {
            if (!isAuthReady || !userId) return;
            const subjectsDocRef = doc(db, `artifacts/${appId}/users/${userId}/appData/subjects`);
            try {
                await setDoc(subjectsDocRef, { list: JSON.stringify(allSubjects) });
                subjectError.classList.add('hidden');
            } catch (e) {
                console.error("Error saving subjects: ", e);
                subjectError.textContent = "Subject save nahi ho paya. Dobara koshish karein.";
                subjectError.classList.remove('hidden');
            }
        }

        addSubjectBtn.addEventListener('click', async () => {
            const newSubject = newSubjectInput.value.trim();
            if (newSubject) {
                if (!allSubjects.find(s => s.toLowerCase() === newSubject.toLowerCase())) {
                    allSubjects.push(newSubject);
                    await saveSubjects();
                    populateSubjectDropdowns(); // Update UI directly
                    newSubjectInput.value = '';
                    subjectError.classList.add('hidden');
                } else {
                    subjectError.textContent = "Yeh subject pehle se maujood hai.";
                    subjectError.classList.remove('hidden');
                }
            }
        });

        removeSubjectBtn.addEventListener('click', async () => {
             const selectedSubjectToRemove = subjectSelect.value;
            if (selectedSubjectToRemove) {
                allSubjects = allSubjects.filter(sub => sub !== selectedSubjectToRemove);
                await saveSubjects();
                populateSubjectDropdowns(); // Update UI directly
                subjectError.classList.add('hidden');
            } else {
                subjectError.textContent = "Kripya hatane ke liye form mein se koi subject chunein.";
                subjectError.classList.remove('hidden');
            }
        });

        // --- Form Submission (Add Result) ---
        resultForm.addEventListener('submit', async function(event) {
            event.preventDefault();
            if (!isAuthReady || !userId) return;

            const studentName = document.getElementById('studentName').value.trim();
            const studentClass = document.getElementById('studentClass').value.trim();
            const studentField = document.getElementById('studentField').value.trim();
            const subject = subjectSelect.value;
            const topicName = document.getElementById('topicName').value.trim(); 
            const score = document.getElementById('score').value.trim();
            const totalMarks = document.getElementById('totalMarks').value.trim();
            const resultDate = document.getElementById('resultDate').value.trim();

            if (!studentName || !studentClass || !studentField || !subject || !topicName || !score || !totalMarks || !resultDate) {
                errorMessage.classList.remove('hidden');
                return;
            }
            errorMessage.classList.add('hidden');

            try {
                const resultsCollectionRef = collection(db, `artifacts/${appId}/users/${userId}/results`);
                await addDoc(resultsCollectionRef, {
                    studentName, studentClass, studentField, subject, topicName, 
                    score: parseInt(score, 10),
                    totalMarks: parseInt(totalMarks, 10),
                    resultDate,
                    timestamp: new Date()
                });
                resultForm.reset();
                subjectSelect.selectedIndex = 0;
                studentField.selectedIndex = 0;
                studentClass.selectedIndex = 0;
            } catch (e) {
                console.error("Error adding document: ", e);
            }
        });
        
        // --- Event Delegation for Table Buttons (View, Edit, Delete) ---
        resultsTableBody.addEventListener('click', async function(event) {
            const target = event.target;
            const docId = target.dataset.id;
            if (!docId || !isAuthReady || !userId) return;

            const resultData = currentResults.find(r => r.id === docId);
            if (!resultData) return;

            if (target.classList.contains('delete-btn')) {
                if (window.confirm("Kya aap is result ko delete karna chahte hain?")) {
                    try {
                        await deleteDoc(doc(db, `artifacts/${appId}/users/${userId}/results`, docId));
                    } catch (e) {
                        console.error("Error deleting document: ", e);
                    }
                }
            } else if (target.classList.contains('edit-btn')) {
                editDocId.value = docId;
                editStudentName.value = resultData.studentName;
                editStudentClass.value = resultData.studentClass;
                editStudentField.value = resultData.studentField;
                editSubject.value = resultData.subject;
                editTopicName.value = resultData.topicName;
                editScore.value = resultData.score;
                editTotalMarks.value = resultData.totalMarks;
                editResultDate.value = resultData.resultDate;
                showModal(editModal);
            } else if (target.classList.contains('view-card-btn')) {
                const percentage = resultData.totalMarks > 0 ? ((resultData.score / resultData.totalMarks) * 100).toFixed(2) : 'N/A';
                resultCardContent.innerHTML = `
                    <div class="text-center pb-4 result-card-header">
                        <h3 class="text-2xl font-bold text-gray-800">Result Card</h3>
                        <p class="text-gray-500">${new Date(resultData.resultDate).toLocaleDateString('en-GB', { day: 'numeric', month: 'long', year: 'numeric' })}</p>
                    </div>
                    <div class="mt-6 space-y-3">
                        <div class="result-card-item"><span class="font-semibold text-gray-600">Naam:</span> <span class="font-medium text-gray-800">${resultData.studentName}</span></div>
                        <div class="result-card-item"><span class="font-semibold text-gray-600">Class:</span> <span class="font-medium text-gray-800">${resultData.studentClass}</span></div>
                        <div class="result-card-item"><span class="font-semibold text-gray-600">Field:</span> <span class="font-medium text-gray-800">${resultData.studentField}</span></div>
                        <div class="result-card-item"><span class="font-semibold text-gray-600">Subject:</span> <span class="font-medium text-gray-800">${resultData.subject}</span></div>
                        <div class="result-card-item"><span class="font-semibold text-gray-600">Topic:</span> <span class="font-medium text-gray-800">${resultData.topicName}</span></div>
                        <div class="result-card-item"><span class="font-semibold text-gray-600">Score:</span> <span class="font-medium text-gray-800">${resultData.score} / ${resultData.totalMarks}</span></div>
                        <div class="result-card-item"><span class="font-semibold text-gray-600">Percentage:</span> <span class="font-bold text-xl text-blue-600">${percentage}%</span></div>
                    </div>
                `;
                showModal(cardModal);
            }
        });

        // --- Edit Form Submission ---
        editForm.addEventListener('submit', async function(event) {
            event.preventDefault();
            const docId = editDocId.value;
            if (!docId || !isAuthReady || !userId) return;

            const updatedData = {
                studentName: editStudentName.value,
                studentClass: editStudentClass.value,
                studentField: editStudentField.value,
                subject: editSubject.value,
                topicName: editTopicName.value,
                score: parseInt(editScore.value, 10),
                totalMarks: parseInt(editTotalMarks.value, 10),
                resultDate: editResultDate.value
            };

            try {
                const docRef = doc(db, `artifacts/${appId}/users/${userId}/results`, docId);
                await updateDoc(docRef, updatedData);
                hideModal(editModal);
            } catch (e) {
                console.error("Error updating document: ", e);
            }
        });
        
        // --- Search Functionality ---
        searchInput.addEventListener('keyup', () => {
            const searchTerm = searchInput.value.toLowerCase();
            const rows = resultsTableBody.getElementsByTagName('tr');
            for (let row of rows) {
                const studentName = row.dataset.studentName;
                if (studentName.includes(searchTerm)) {
                    row.style.display = '';
                } else {
                    row.style.display = 'none';
                }
            }
        });

        // --- Modal Closing ---
        closeCardModalBtn.addEventListener('click', () => hideModal(cardModal));
        closeEditModalBtn.addEventListener('click', () => hideModal(editModal));
        closeFinalResultModalBtn.addEventListener('click', () => hideModal(finalResultModal));
        [cardModal, editModal, finalResultModal].forEach(modal => {
            modal.addEventListener('click', (e) => {
                if (e.target === modal) {
                    hideModal(modal);
                }
            });
        });

        // --- PDF and Excel Export ---
        const { jsPDF } = window.jspdf;
        exportPdfBtn.addEventListener('click', () => {
            html2canvas(resultsTable).then(canvas => {
                const imgData = canvas.toDataURL('image/png');
                const pdf = new jsPDF('l', 'mm', 'a4');
                const pdfWidth = pdf.internal.pageSize.getWidth();
                const pdfHeight = pdf.internal.pageSize.getHeight();
                const canvasWidth = canvas.width;
                const canvasHeight = canvas.height;
                const ratio = canvasWidth / canvasHeight;
                const width = pdfWidth - 20;
                const height = width / ratio;
                pdf.addImage(imgData, 'PNG', 10, 10, width, height);
                pdf.save("student-results.pdf");
            });
        });

        exportCardPdfBtn.addEventListener('click', () => {
            html2canvas(resultCardContent).then(canvas => {
                const imgData = canvas.toDataURL('image/png');
                const pdf = new jsPDF('p', 'mm', 'a5');
                const pdfWidth = pdf.internal.pageSize.getWidth();
                const canvasWidth = canvas.width;
                const canvasHeight = canvas.height;
                const ratio = canvasWidth / canvasHeight;
                const width = pdfWidth - 20;
                const height = width / ratio;
                pdf.addImage(imgData, 'PNG', 10, 10, width, height);
                pdf.save("result-card.pdf");
            });
        });

        exportExcelBtn.addEventListener('click', () => {
            let csvContent = "data:text/csv;charset=utf-8,";
            const headers = ["Student Name", "Class", "Field", "Subject", "Topic", "Score", "Total Marks", "Percentage", "Date"];
            csvContent += headers.join(",") + "\r\n";

            currentResults.forEach(result => {
                const percentage = result.totalMarks > 0 ? ((result.score / result.totalMarks) * 100).toFixed(2) : 'N/A';
                const row = [
                    result.studentName,
                    result.studentClass,
                    result.studentField,
                    result.subject,
                    result.topicName,
                    result.score,
                    result.totalMarks,
                    percentage + '%',
                    result.resultDate
                ];
                csvContent += row.join(",") + "\r\n";
            });

            const encodedUri = encodeURI(csvContent);
            const link = document.createElement("a");
            link.setAttribute("href", encodedUri);
            link.setAttribute("download", "student_results.csv");
            document.body.appendChild(link);
            link.click();
            document.body.removeChild(link);
        });

        // --- Final Results Logic ---
        function calculateGrade(percentage) {
            if (percentage >= 90) return 'A+';
            if (percentage >= 80) return 'A';
            if (percentage >= 70) return 'B';
            if (percentage >= 60) return 'C';
            if (percentage >= 50) return 'D';
            return 'F';
        }

        function displayFinalResults(filter = 'All') {
            const finalResults = {};
            
            const filteredData = currentResults.filter(result => {
                if (filter === 'All') return true;
                return result.studentField === filter;
            });

            filteredData.forEach(result => {
                const key = `${result.studentName}-${result.studentClass}-${result.studentField}`;
                if (!finalResults[key]) {
                    finalResults[key] = {
                        studentName: result.studentName,
                        studentClass: result.studentClass,
                        studentField: result.studentField,
                        totalScore: 0,
                        totalMarks: 0,
                    };
                }
                finalResults[key].totalScore += result.score;
                finalResults[key].totalMarks += result.totalMarks;
            });

            finalResultTableBody.innerHTML = '';
            Object.values(finalResults).forEach(data => {
                const overallPercentage = data.totalMarks > 0 ? ((data.totalScore / data.totalMarks) * 100).toFixed(2) : 0;
                const grade = calculateGrade(overallPercentage);
                const row = `
                    <tr class="border-b border-gray-200 hover:bg-gray-50">
                        <td class="py-3 px-4">${data.studentName}</td>
                        <td class="py-3 px-4">${data.studentClass}</td>
                        <td class="py-3 px-4">${data.studentField}</td>
                        <td class="py-3 px-4">${data.totalScore}</td>
                        <td class="py-3 px-4">${data.totalMarks}</td>
                        <td class="py-3 px-4 font-semibold">${overallPercentage}%</td>
                        <td class="py-3 px-4 font-bold text-lg ${grade === 'F' ? 'text-red-500' : 'text-green-600'}">${grade}</td>
                    </tr>
                `;
                finalResultTableBody.innerHTML += row;
            });
        }

        showFinalResultBtn.addEventListener('click', () => {
            displayFinalResults();
            showModal(finalResultModal);
        });

        finalResultFilters.addEventListener('click', (e) => {
            if (e.target.classList.contains('filter-btn')) {
                const filter = e.target.dataset.filter;
                // Update active button style
                finalResultFilters.querySelectorAll('.filter-btn').forEach(btn => {
                    btn.classList.remove('bg-blue-500', 'hover:bg-blue-600');
                    btn.classList.add('bg-gray-500', 'hover:bg-gray-600');
                });
                e.target.classList.remove('bg-gray-500', 'hover:bg-gray-600');
                e.target.classList.add('bg-blue-500', 'hover:bg-blue-600');
                
                displayFinalResults(filter);
            }
        });

        exportFinalResultPdfBtn.addEventListener('click', () => {
             html2canvas(document.getElementById('finalResultTable')).then(canvas => {
                const imgData = canvas.toDataURL('image/png');
                const pdf = new jsPDF('p', 'mm', 'a4');
                const pdfWidth = pdf.internal.pageSize.getWidth();
                const pdfHeight = pdf.internal.pageSize.getHeight();
                const canvasWidth = canvas.width;
                const canvasHeight = canvas.height;
                const ratio = canvasWidth / canvasHeight;
                const width = pdfWidth - 20;
                const height = width / ratio;
                pdf.addImage(imgData, 'PNG', 10, 10, width, height > pdfHeight - 20 ? pdfHeight - 20 : height);
                pdf.save("final-consolidated-results.pdf");
            });
        });

    </script>
</body>
</html>
