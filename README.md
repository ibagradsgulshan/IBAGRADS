<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Student Result Manager (Professional)</title>
    <!-- Tailwind CSS CDN -->
    <script src="https://cdn.tailwindcss.com"></script>
    <!-- Google Fonts: Inter -->
    <link rel="preconnect" href="https://fonts.googleapis.com">
    <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600;700&display=swap" rel="stylesheet">
    <!-- PDF Generation Libraries -->
    <script src="https://cdnjs.cloudflare.com/ajax/libs/html2canvas/1.4.1/html2canvas.min.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/jspdf/2.5.1/jspdf.umd.min.js"></script>
    <!-- Lucide Icons -->
    <script src="https://unpkg.com/lucide@latest"></script>
    <style>
        body {
            font-family: 'Inter', sans-serif;
            background-color: #f0f2f5;
        }
        .modal-overlay {
            position: fixed;
            top: 0; left: 0; width: 100%; height: 100%;
            background-color: rgba(0, 0, 0, 0.6);
            display: flex; justify-content: center; align-items: center;
            z-index: 1000; opacity: 0; visibility: hidden;
            transition: opacity 0.3s ease, visibility 0.3s ease;
        }
        .modal-overlay.active { opacity: 1; visibility: visible; }
        .modal-container {
            background-color: white;
            padding: 2rem;
            border-radius: 0.75rem;
            box-shadow: 0 10px 25px -5px rgba(0,0,0,0.1), 0 8px 10px -6px rgba(0,0,0,0.1);
            max-height: 90vh;
            overflow-y: auto;
            transform: scale(0.95);
            transition: transform 0.3s ease;
        }
        .modal-overlay.active .modal-container { transform: scale(1); }
        .printable-background {
            background: linear-gradient(135deg, #f8f9fa 0%, #e9ecef 100%);
        }
    </style>
</head>
<body class="bg-slate-100 flex items-center justify-center min-h-screen p-4 sm:p-6">

    <div class="w-full max-w-6xl mx-auto bg-white rounded-2xl shadow-xl p-6 sm:p-8">
        
        <!-- Header Section -->
        <div class="flex items-center justify-between mb-8 pb-4 border-b border-gray-200">
            <div class="flex items-center gap-4">
                <div class="bg-blue-100 p-3 rounded-full">
                    <svg xmlns="http://www.w3.org/2000/svg" width="32" height="32" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="text-blue-600"><path d="M4 19.5v-15A2.5 2.5 0 0 1 6.5 2H20v20H6.5a2.5 2.5 0 0 1 0-5H20"></path></svg>
                </div>
                <div>
                    <h1 class="text-2xl font-bold text-gray-800">IBAGRADS XI-XII</h1>
                    <p class="text-sm text-gray-500">Student Result Management System</p>
                </div>
            </div>
            <div class="text-right">
                <p id="currentDate" class="font-medium text-gray-700"></p>
                <p class="text-sm text-gray-500">Karachi, Sindh</p>
            </div>
        </div>
        
        <!-- Form & Subject Management in a Grid -->
        <div class="grid grid-cols-1 lg:grid-cols-5 gap-8 mb-8">
            <div class="lg:col-span-3 bg-gray-50 p-6 rounded-xl border border-gray-200">
                <h2 class="text-xl font-semibold text-gray-700 mb-4">Naya Result Add Karein</h2>
                <form id="resultForm" class="grid grid-cols-1 md:grid-cols-2 gap-4 items-end">
                    <div>
                        <label for="studentName" class="block text-sm font-medium text-gray-600 mb-1">Student ka Naam</label>
                        <input type="text" id="studentName" placeholder="Jaise: Anil Kumar" class="w-full px-4 py-2 bg-white border border-gray-300 rounded-lg focus:ring-2 focus:ring-blue-500 transition" required>
                    </div>
                    <div>
                        <label for="studentClass" class="block text-sm font-medium text-gray-600 mb-1">Class</label>
                        <select id="studentClass" class="w-full px-4 py-2 bg-white border border-gray-300 rounded-lg focus:ring-2 focus:ring-blue-500 transition" required>
                            <option value="" disabled selected>Select Class</option>
                            <option value="XI">XI</option>
                            <option value="XII">XII</option>
                        </select>
                    </div>
                    <div>
                        <label for="subject" class="block text-sm font-medium text-gray-600 mb-1">Subject</label>
                        <select id="subject" class="w-full px-4 py-2 bg-white border border-gray-300 rounded-lg focus:ring-2 focus:ring-blue-500 transition" required>
                            <option value="" disabled selected>Select a Subject</option>
                        </select>
                    </div>
                    <div>
                        <label for="testType" class="block text-sm font-medium text-gray-600 mb-1">Test Type</label>
                        <select id="testType" class="w-full px-4 py-2 bg-white border border-gray-300 rounded-lg focus:ring-2 focus:ring-blue-500 transition" required>
                            <option value="Weekly Test">Weekly Test</option>
                            <option value="Monthly Test">Monthly Test</option>
                            <option value="Yearly Test">Yearly Test</option>
                        </select>
                    </div>
                     <div>
                        <label for="topicName" class="block text-sm font-medium text-gray-600 mb-1">Topic ka Naam</label>
                        <input type="text" id="topicName" placeholder="Jaise: Algebra" class="w-full px-4 py-2 bg-white border border-gray-300 rounded-lg focus:ring-2 focus:ring-blue-500 transition" required>
                    </div>
                    <div>
                        <label for="score" class="block text-sm font-medium text-gray-600 mb-1">Score</label>
                        <input type="number" id="score" placeholder="Jaise: 85" min="0" class="w-full px-4 py-2 bg-white border border-gray-300 rounded-lg focus:ring-2 focus:ring-blue-500 transition" required>
                    </div>
                    <div>
                        <label for="totalMarks" class="block text-sm font-medium text-gray-600 mb-1">Total Marks</label>
                        <input type="number" id="totalMarks" value="100" min="1" class="w-full px-4 py-2 bg-white border border-gray-300 rounded-lg focus:ring-2 focus:ring-blue-500 transition" required>
                    </div>
                    <div>
                        <label for="resultDate" class="block text-sm font-medium text-gray-600 mb-1">Date</label>
                        <input type="date" id="resultDate" class="w-full px-4 py-2 bg-white border border-gray-300 rounded-lg focus:ring-2 focus:ring-blue-500 transition" required>
                    </div>
                    <div class="md:col-span-2">
                        <button type="submit" class="w-full bg-blue-600 text-white font-semibold py-2.5 px-4 rounded-lg hover:bg-blue-700 flex items-center justify-center gap-2 transition duration-300">
                            <i data-lucide="plus-circle" class="w-5 h-5"></i> Add Result
                        </button>
                    </div>
                </form>
            </div>

            <div class="lg:col-span-2 bg-gray-50 p-6 rounded-xl border border-gray-200">
                <h2 class="text-xl font-semibold text-gray-700 mb-4">Subjects Manage Karein</h2>
                <div class="space-y-4">
                    <input type="text" id="newSubjectInput" placeholder="Naya Subject Add Karein" class="w-full px-4 py-2 bg-white border border-gray-300 rounded-lg focus:ring-2 focus:ring-blue-500 transition">
                    <div class="flex flex-col sm:flex-row gap-2">
                        <button id="addSubjectBtn" class="w-full bg-green-500 text-white font-semibold py-2 px-4 rounded-lg hover:bg-green-600 flex items-center justify-center gap-2 transition duration-300">
                            <i data-lucide="plus" class="w-5 h-5"></i> Add
                        </button>
                        <button id="removeSubjectBtn" class="w-full bg-red-500 text-white font-semibold py-2 px-4 rounded-lg hover:bg-red-600 flex items-center justify-center gap-2 transition duration-300">
                           <i data-lucide="trash-2" class="w-5 h-5"></i> Remove
                        </button>
                    </div>
                </div>
                 <p id="subjectError" class="text-red-600 text-sm mt-2 hidden"></p>
            </div>
        </div>

        <!-- Results Table Section -->
        <div class="overflow-x-auto">
            <div class="flex flex-col sm:flex-row justify-between items-center mb-4 gap-4">
                <h2 class="text-xl font-semibold text-gray-700">Uploaded Results</h2>
                <div class="flex flex-wrap items-center justify-center sm:justify-end gap-2">
                    <input type="text" id="searchInput" placeholder="Student ke naam se khojein..." class="w-full sm:w-48 px-4 py-2 bg-white border border-gray-300 rounded-lg focus:ring-2 focus:ring-blue-500 transition">
                    <button id="showFinalResultBtn" class="bg-indigo-600 text-white font-semibold py-2 px-4 rounded-lg hover:bg-indigo-700 transition flex items-center gap-2"><i data-lucide="award" class="w-5 h-5"></i> Final Results</button>
                    <button id="exportPdfBtn" class="bg-red-600 text-white font-semibold py-2 px-4 rounded-lg hover:bg-red-700 transition flex items-center gap-2"><i data-lucide="file-text" class="w-5 h-5"></i> PDF</button>
                    <button id="exportExcelBtn" class="bg-green-600 text-white font-semibold py-2 px-4 rounded-lg hover:bg-green-700 transition flex items-center gap-2"><i data-lucide="file-spreadsheet" class="w-5 h-5"></i> Excel</button>
                </div>
            </div>

            <table id="resultsTable" class="min-w-full bg-white rounded-lg shadow-md overflow-hidden">
                <thead class="bg-gray-100 text-gray-600">
                    <tr>
                        <th class="text-left py-3 px-4 uppercase font-semibold text-sm">Student Naam</th>
                        <th class="text-left py-3 px-4 uppercase font-semibold text-sm">Class</th>
                        <th class="text-left py-3 px-4 uppercase font-semibold text-sm">Subject</th>
                        <th class="text-left py-3 px-4 uppercase font-semibold text-sm">Test Type</th>
                        <th class="text-left py-3 px-4 uppercase font-semibold text-sm">Score</th>
                        <th class="text-left py-3 px-4 uppercase font-semibold text-sm">Date</th>
                        <th class="text-center py-3 px-4 uppercase font-semibold text-sm">Actions</th>
                    </tr>
                </thead>
                <tbody id="resultsTableBody" class="text-gray-700 divide-y divide-gray-200"></tbody>
            </table>
            <div id="noResultsMessage" class="text-center py-10 text-gray-500 hidden"><p>Abhi tak koi result upload nahi hua hai.</p></div>
        </div>
    </div>

    <!-- Modals -->
    <div id="cardModal" class="modal-overlay"><div id="cardModalContainer" class="modal-container w-full max-w-2xl"></div></div>
    <div id="editModal" class="modal-overlay"><div id="editModalContainer" class="modal-container w-full max-w-lg"></div></div>
    <div id="finalResultModal" class="modal-overlay"><div id="finalResultModalContainer" class="modal-container w-full max-w-5xl"></div></div>
    <div id="confirmModal" class="modal-overlay">
        <div class="modal-container w-full max-w-sm">
            <h3 id="confirmTitle" class="text-lg font-bold text-gray-800">Confirm Action</h3>
            <p id="confirmMessage" class="text-gray-600 mt-2 mb-6">Are you sure?</p>
            <div class="flex justify-end space-x-2">
                <button id="confirmBtn" class="bg-red-600 text-white font-semibold py-2 px-4 rounded-lg hover:bg-red-700">Confirm</button>
                <button id="cancelBtn" class="bg-gray-300 text-gray-800 font-semibold py-2 px-4 rounded-lg hover:bg-gray-400">Cancel</button>
            </div>
        </div>
    </div>
    
    <script>
        document.addEventListener('DOMContentLoaded', () => {
            lucide.createIcons();
            
            // --- Global Variables & Constants ---
            const { jsPDF } = window.jspdf;
            document.getElementById('currentDate').textContent = new Date().toLocaleDateString('en-GB', { day: 'numeric', month: 'long', year: 'numeric' });

            // Form & UI Elements
            const resultForm = document.getElementById('resultForm');
            const resultsTableBody = document.getElementById('resultsTableBody');
            const noResultsMessage = document.getElementById('noResultsMessage');
            const searchInput = document.getElementById('searchInput'); 
            const subjectSelect = document.getElementById('subject');
            const newSubjectInput = document.getElementById('newSubjectInput');
            const addSubjectBtn = document.getElementById('addSubjectBtn');
            const removeSubjectBtn = document.getElementById('removeSubjectBtn');
            const subjectError = document.getElementById('subjectError');

            let currentResults = [];
            let subjects = [];

            // --- Data Persistence (Local Storage) ---
            const saveResultsToLocal = () => localStorage.setItem('studentResults', JSON.stringify(currentResults));
            const loadResultsFromLocal = () => currentResults = JSON.parse(localStorage.getItem('studentResults')) || [];
            const saveSubjectsToLocal = () => localStorage.setItem('studentSubjects', JSON.stringify(subjects));
            const loadSubjectsFromLocal = () => {
                subjects = JSON.parse(localStorage.getItem('studentSubjects')) || ["Physics", "Chemistry", "Maths", "English", "Biology"];
                if (subjects.length === 0) subjects = ["Physics", "Chemistry", "Maths", "English", "Biology"];
            };

            // --- Utilities ---
            const showModal = (modalId) => document.getElementById(modalId).classList.add('active');
            const hideModal = (modalId) => document.getElementById(modalId).classList.remove('active');
            const calculateGrade = (p) => {
                if (p >= 90) return 'A+'; if (p >= 80) return 'A'; if (p >= 70) return 'B';
                if (p >= 60) return 'C'; if (p >= 50) return 'D'; return 'F';
            };
            const getRemarks = (grade) => {
                const remarks = { 'A+': 'Outstanding Performance.', 'A': 'Excellent Work.', 'B': 'Good Effort.', 'C': 'Satisfactory.', 'D': 'Needs Improvement.', 'F': 'Requires Serious Attention.' };
                return remarks[grade] || 'N/A';
            };

            // --- Rendering ---
            const renderResults = () => {
                resultsTableBody.innerHTML = '';
                const sorted = [...currentResults].sort((a, b) => new Date(b.timestamp) - new Date(a.timestamp));
                sorted.forEach(data => {
                    const row = resultsTableBody.insertRow();
                    row.dataset.id = data.id;
                    row.dataset.studentName = data.studentName.toLowerCase();
                    const percentage = data.totalMarks > 0 ? ((data.score / data.totalMarks) * 100).toFixed(2) : 'N/A';
                    row.innerHTML = `
                        <td class="py-3 px-4 font-medium">${data.studentName}</td>
                        <td class="py-3 px-4">${data.studentClass}</td>
                        <td class="py-3 px-4">${data.subject}</td>
                        <td class="py-3 px-4">${data.testType || 'N/A'}</td>
                        <td class="py-3 px-4">${data.score} / ${data.totalMarks} (${percentage}%)</td>
                        <td class="py-3 px-4">${new Date(data.resultDate).toLocaleDateString()}</td>
                        <td class="py-3 px-4">
                            <div class="flex items-center justify-center space-x-2">
                                <button data-id="${data.id}" class="view-card-btn p-2 text-blue-600 hover:bg-blue-100 rounded-full transition"><i data-lucide="eye" class="w-5 h-5"></i></button>
                                <button data-id="${data.id}" class="edit-btn p-2 text-yellow-600 hover:bg-yellow-100 rounded-full transition"><i data-lucide="pencil" class="w-5 h-5"></i></button>
                                <button data-id="${data.id}" class="delete-btn p-2 text-red-600 hover:bg-red-100 rounded-full transition"><i data-lucide="trash-2" class="w-5 h-5"></i></button>
                            </div>
                        </td>`;
                });
                lucide.createIcons();
                noResultsMessage.classList.toggle('hidden', currentResults.length > 0);
                 if (currentResults.length === 0) noResultsMessage.innerHTML = `<p>Abhi tak koi result upload nahi hua hai.</p>`
            };

            const populateSubjectDropdowns = () => {
                const editSubjectEl = document.getElementById('editSubject');
                [subjectSelect, editSubjectEl].forEach(selectEl => {
                    if (!selectEl) return;
                    const currentValue = selectEl.value;
                    selectEl.innerHTML = '<option value="" disabled>Select</option>';
                    subjects.forEach(s => selectEl.innerHTML += `<option value="${s}">${s}</option>`);
                    selectEl.value = currentValue;
                     if (!selectEl.value) {
                       selectEl.selectedIndex = 0;
                    }
                });
            };

            // --- Subject Management (Local Storage) ---
            addSubjectBtn.addEventListener('click', () => {
                const newSubject = newSubjectInput.value.trim();
                if (!newSubject) {
                    subjectError.textContent = "Please enter a subject name.";
                    subjectError.classList.remove('hidden'); return;
                }
                if (subjects.find(s => s.toLowerCase() === newSubject.toLowerCase())) {
                     subjectError.textContent = "Subject already exists.";
                     subjectError.classList.remove('hidden'); return;
                }
                subjects.push(newSubject);
                saveSubjectsToLocal();
                populateSubjectDropdowns();
                newSubjectInput.value = '';
                subjectError.classList.add('hidden');
            });

            removeSubjectBtn.addEventListener('click', () => {
                const selected = subjectSelect.value;
                if (!selected) {
                    subjectError.textContent = "Please select a subject to remove.";
                    subjectError.classList.remove('hidden'); return;
                }
                subjects = subjects.filter(s => s !== selected);
                saveSubjectsToLocal();
                populateSubjectDropdowns();
                subjectError.classList.add('hidden');
            });
            
            // --- Add/Edit Result Forms (Local Storage) ---
            resultForm.addEventListener('submit', (e) => {
                e.preventDefault();
                const newResult = {
                    id: crypto.randomUUID(),
                    studentName: document.getElementById('studentName').value.trim(),
                    studentClass: document.getElementById('studentClass').value,
                    subject: subjectSelect.value,
                    testType: document.getElementById('testType').value,
                    topicName: document.getElementById('topicName').value.trim(),
                    score: parseInt(document.getElementById('score').value, 10),
                    totalMarks: parseInt(document.getElementById('totalMarks').value, 10),
                    resultDate: document.getElementById('resultDate').value,
                    timestamp: new Date().toISOString()
                };
                currentResults.push(newResult);
                saveResultsToLocal();
                renderResults();
                resultForm.reset();
                document.getElementById('studentClass').selectedIndex = 0;
                subjectSelect.selectedIndex = 0;
            });
            
            // --- Table Actions (View, Edit, Delete) ---
            resultsTableBody.addEventListener('click', (e) => {
                const btn = e.target.closest('button');
                if (!btn) return;
                const id = btn.dataset.id;
                
                if (btn.classList.contains('delete-btn')) {
                    showConfirmationModal(
                        'Delete Result',
                        'Kya aap is result ko delete karna chahte hain? This action cannot be undone.',
                        () => {
                            currentResults = currentResults.filter(r => r.id !== id);
                            saveResultsToLocal();
                            renderResults();
                        }
                    );
                } else if (btn.classList.contains('view-card-btn')) {
                    const result = currentResults.find(r => r.id === id);
                    if(result) {
                        const studentResults = currentResults.filter(r => r.studentName.toLowerCase() === result.studentName.toLowerCase() && r.studentClass === result.studentClass);
                        showResultCard(studentResults);
                    }
                } else if (btn.classList.contains('edit-btn')) {
                    const data = currentResults.find(r => r.id === id);
                    if (data) showEditModal(data);
                }
            });

            // --- Consolidated Result Card ---
            const createConsolidatedCardHTML = (results) => {
                if (!results || results.length === 0) return '';
                const student = results[0];
                const totalScore = results.reduce((sum, r) => sum + r.score, 0);
                const totalMarks = results.reduce((sum, r) => sum + r.totalMarks, 0);
                const overallPercentage = totalMarks > 0 ? ((totalScore / totalMarks) * 100).toFixed(2) : 0;
                const overallGrade = calculateGrade(overallPercentage);
                const issueDate = new Date().toLocaleDateString('en-GB', { day: 'numeric', month: 'long', year: 'numeric' });

                const resultsRowsHTML = results.map(data => {
                    const percentage = data.totalMarks > 0 ? ((data.score / data.totalMarks) * 100).toFixed(2) : 0;
                    return `
                        <tr>
                            <td class="border p-2">${data.subject}</td>
                            <td class="border p-2">${data.testType}</td>
                            <td class="border p-2">${data.topicName}</td>
                            <td class="border p-2 text-center">${data.score}</td>
                            <td class="border p-2 text-center">${data.totalMarks}</td>
                            <td class="border p-2 text-center font-semibold">${percentage}%</td>
                        </tr>`;
                }).join('');

                return `
                <div class="p-8 border-2 border-gray-800 bg-white relative">
                    <div class="absolute inset-0 flex items-center justify-center z-0"><p class="text-gray-200 text-8xl font-black select-none opacity-50">IBAGRADS</p></div>
                    <div class="relative z-10">
                        <div class="flex items-center justify-between pb-4 border-b-2 border-gray-800">
                             <div class="flex items-center gap-3">
                                <svg xmlns="http://www.w3.org/2000/svg" width="40" height="40" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="text-blue-600"><path d="M4 19.5v-15A2.5 2.5 0 0 1 6.5 2H20v20H6.5a2.5 2.5 0 0 1 0-5H20"></path></svg>
                                <div><h3 class="text-xl font-bold text-gray-800">IBAGRADS XI-XII</h3><p class="text-sm font-medium text-gray-500">OFFICIAL RESULT CARD</p></div>
                            </div>
                        </div>
                        <div class="grid grid-cols-2 gap-x-8 gap-y-4 my-6 text-sm">
                            <p><strong>Student Name:</strong> ${student.studentName}</p>
                            <p><strong>Class:</strong> ${student.studentClass}</p>
                            <p><strong>Date of Issue:</strong> ${issueDate}</p>
                            <p><strong>Total Tests Taken:</strong> ${results.length}</p>
                        </div>
                        <table class="w-full text-sm border-collapse">
                            <thead class="bg-gray-100 text-gray-600">
                                <tr>
                                    <th class="border p-2 text-left">Subject</th>
                                    <th class="border p-2 text-left">Test Type</th>
                                    <th class="border p-2 text-left">Topic</th>
                                    <th class="border p-2 text-center">Score</th>
                                    <th class="border p-2 text-center">Total Marks</th>
                                    <th class="border p-2 text-center">Percentage</th>
                                </tr>
                            </thead>
                            <tbody>${resultsRowsHTML}</tbody>
                        </table>
                        <div class="grid grid-cols-3 gap-4 mt-6 text-center bg-gray-50 p-4 rounded-lg">
                            <div><p class="text-sm text-gray-600">Overall Percentage</p><p class="text-2xl font-bold text-blue-600">${overallPercentage}%</p></div>
                            <div><p class="text-sm text-gray-600">Grade</p><p class="text-2xl font-bold text-blue-600">${overallGrade}</p></div>
                            <div><p class="text-sm text-gray-600">Remarks</p><p class="text-lg font-semibold text-blue-600">${getRemarks(overallGrade)}</p></div>
                        </div>
                        <div class="mt-12 pt-4 border-t text-center text-xs text-gray-500">
                            <p>This is a computer-generated document. | IBAGRADS XI-XII, Karachi, Sindh, Pakistan</p>
                        </div>
                    </div>
                </div>`;
            };

            const showResultCard = (results) => {
                const container = document.getElementById('cardModalContainer');
                container.innerHTML = `
                    <div id="printableCard">${createConsolidatedCardHTML(results)}</div>
                    <div class="flex justify-end space-x-2 mt-4">
                        <button id="exportCardPdfBtn" class="bg-blue-600 text-white font-semibold py-2 px-4 rounded-lg hover:bg-blue-700 transition">Export PDF</button>
                        <button id="closeCardModalBtn" class="bg-gray-300 text-gray-800 font-semibold py-2 px-4 rounded-lg hover:bg-gray-400 transition">Band Karein</button>
                    </div>`;
                document.getElementById('closeCardModalBtn').onclick = () => hideModal('cardModal');
                document.getElementById('exportCardPdfBtn').onclick = () => exportToPdf(document.getElementById('printableCard'), `${results[0].studentName}_result.pdf`, 'portrait');
                showModal('cardModal');
            };

            // --- Edit Modal ---
            const showEditModal = (data) => {
                const container = document.getElementById('editModalContainer');
                container.innerHTML = `
                <h2 class="text-2xl font-semibold text-gray-700 mb-4">Result Edit Karein</h2>
                <form id="editForm">
                    <input type="hidden" id="editDocId" value="${data.id}">
                    <div class="grid grid-cols-1 md:grid-cols-2 gap-4">
                        <div><label class="block text-sm font-medium">Naam</label><input type="text" id="editStudentName" class="w-full px-4 py-2 bg-white border border-gray-300 rounded-lg" value="${data.studentName}" required></div>
                        <div><label class="block text-sm font-medium">Class</label><select id="editStudentClass" class="w-full px-4 py-2 bg-white border border-gray-300 rounded-lg" required><option value="XI">XI</option><option value="XII">XII</option></select></div>
                        <div><label class="block text-sm font-medium">Subject</label><select id="editSubject" class="w-full px-4 py-2 bg-white border border-gray-300 rounded-lg" required></select></div>
                        <div><label class="block text-sm font-medium">Test Type</label><select id="editTestType" class="w-full px-4 py-2 bg-white border border-gray-300 rounded-lg" required><option value="Weekly Test">Weekly Test</option><option value="Monthly Test">Monthly Test</option><option value="Yearly Test">Yearly Test</option></select></div>
                        <div><label class="block text-sm font-medium">Topic</label><input type="text" id="editTopicName" class="w-full px-4 py-2 bg-white border border-gray-300 rounded-lg" value="${data.topicName}" required></div>
                        <div><label class="block text-sm font-medium">Score</label><input type="number" id="editScore" class="w-full px-4 py-2 bg-white border border-gray-300 rounded-lg" value="${data.score}" required></div>
                        <div><label class="block text-sm font-medium">Total Marks</label><input type="number" id="editTotalMarks" class="w-full px-4 py-2 bg-white border border-gray-300 rounded-lg" value="${data.totalMarks}" required></div>
                        <div><label class="block text-sm font-medium">Date</label><input type="date" id="editResultDate" class="w-full px-4 py-2 bg-white border border-gray-300 rounded-lg" value="${data.resultDate}" required></div>
                    </div>
                    <div class="flex justify-end space-x-2 mt-6">
                        <button type="submit" class="bg-blue-600 text-white font-semibold py-2 px-4 rounded-lg hover:bg-blue-700">Save Changes</button>
                        <button type="button" id="closeEditModalBtn" class="bg-gray-300 text-gray-800 font-semibold py-2 px-4 rounded-lg hover:bg-gray-400">Cancel</button>
                    </div>
                </form>`;

                populateSubjectDropdowns();
                document.getElementById('editStudentClass').value = data.studentClass;
                document.getElementById('editSubject').value = data.subject;
                document.getElementById('editTestType').value = data.testType;
                
                document.getElementById('closeEditModalBtn').onclick = () => hideModal('editModal');
                document.getElementById('editForm').onsubmit = (e) => {
                    e.preventDefault();
                    const id = document.getElementById('editDocId').value;
                    const index = currentResults.findIndex(r => r.id === id);
                    if (index > -1) {
                        currentResults[index] = {
                            ...currentResults[index],
                            studentName: document.getElementById('editStudentName').value,
                            studentClass: document.getElementById('editStudentClass').value,
                            subject: document.getElementById('editSubject').value,
                            testType: document.getElementById('editTestType').value,
                            topicName: document.getElementById('editTopicName').value,
                            score: parseInt(document.getElementById('editScore').value, 10),
                            totalMarks: parseInt(document.getElementById('editTotalMarks').value, 10),
                            resultDate: document.getElementById('editResultDate').value,
                        };
                        saveResultsToLocal();
                        renderResults();
                        hideModal('editModal');
                    }
                };
                showModal('editModal');
            };
            
            // --- Confirmation Modal ---
            const showConfirmationModal = (title, message, onConfirm) => {
                document.getElementById('confirmTitle').textContent = title;
                document.getElementById('confirmMessage').textContent = message;

                const confirmBtn = document.getElementById('confirmBtn');
                const cancelBtn = document.getElementById('cancelBtn');

                const confirmHandler = () => { onConfirm(); cleanup(); };
                const cancelHandler = () => cleanup();
                
                const cleanup = () => {
                    hideModal('confirmModal');
                    confirmBtn.removeEventListener('click', confirmHandler);
                    cancelBtn.removeEventListener('click', cancelHandler);
                };

                confirmBtn.addEventListener('click', confirmHandler);
                cancelBtn.addEventListener('click', cancelHandler);
                
                showModal('confirmModal');
            };

            // --- Final Results Modal & Logic ---
            const renderFinalResults = (classFilter = 'All') => {
                const finalResultTableBody = document.querySelector('#finalResultTable tbody');
                if (!finalResultTableBody) return;

                const groupedResults = currentResults.reduce((acc, result) => {
                    const key = `${result.studentName.toLowerCase()}_${result.studentClass}`;
                    if (!acc[key]) {
                        acc[key] = { studentName: result.studentName, studentClass: result.studentClass, totalScore: 0, totalMarks: 0 };
                    }
                    acc[key].totalScore += result.score;
                    acc[key].totalMarks += result.totalMarks;
                    return acc;
                }, {});

                let resultsArray = Object.values(groupedResults);
                if (classFilter !== 'All') {
                    resultsArray = resultsArray.filter(r => r.studentClass === classFilter);
                }
                
                resultsArray.forEach(r => {
                    r.percentage = r.totalMarks > 0 ? ((r.totalScore / r.totalMarks) * 100).toFixed(2) : 0;
                });
                resultsArray.sort((a, b) => b.percentage - a.percentage);

                finalResultTableBody.innerHTML = resultsArray.map(data => {
                    const grade = calculateGrade(data.percentage);
                    return `<tr class="hover:bg-gray-50">
                        <td class="py-3 px-4 font-medium">${data.studentName}</td>
                        <td class="py-3 px-4">${data.studentClass}</td>
                        <td class="py-3 px-4">${data.totalScore}</td>
                        <td class="py-3 px-4">${data.totalMarks}</td>
                        <td class="py-3 px-4 font-semibold text-blue-600">${data.percentage}%</td>
                        <td class="py-3 px-4">${grade}</td>
                    </tr>`;
                }).join('');
            };

            document.getElementById('showFinalResultBtn').addEventListener('click', () => {
                const container = document.getElementById('finalResultModalContainer');
                container.innerHTML = `
                <div id="printableFinalResults" class="p-4 sm:p-6 bg-white rounded-lg">
                    <div class="flex flex-col sm:flex-row justify-between items-start sm:items-center mb-6 pb-4 border-b">
                         <div>
                            <h2 class="text-2xl font-bold text-gray-800">Final Consolidated Results</h2>
                            <p class="text-sm text-gray-500">Summary of student performance</p>
                        </div>
                         <div id="finalResultFilters" class="flex items-center space-x-2 mt-4 sm:mt-0">
                            <button data-filter="XI" class="filter-btn bg-gray-200 text-gray-700 font-semibold py-1 px-3 rounded-md hover:bg-gray-300 transition text-sm">Class XI</button>
                            <button data-filter="XII" class="filter-btn bg-gray-200 text-gray-700 font-semibold py-1 px-3 rounded-md hover:bg-gray-300 transition text-sm">Class XII</button>
                            <button data-filter="All" class="filter-btn bg-blue-600 text-white font-semibold py-1 px-3 rounded-md transition text-sm">All Classes</button>
                        </div>
                    </div>
                    <div class="overflow-x-auto">
                        <table id="finalResultTable" class="min-w-full bg-white">
                            <thead class="bg-gray-100 text-gray-600">
                                <tr>
                                    <th class="text-left py-3 px-4 uppercase font-semibold text-sm">Student</th><th class="text-left py-3 px-4 uppercase font-semibold text-sm">Class</th>
                                    <th class="text-left py-3 px-4 uppercase font-semibold text-sm">Total Score</th><th class="text-left py-3 px-4 uppercase font-semibold text-sm">Total Marks</th>
                                    <th class="text-left py-3 px-4 uppercase font-semibold text-sm">Percentage</th><th class="text-left py-3 px-4 uppercase font-semibold text-sm">Grade</th>
                                </tr>
                            </thead>
                            <tbody class="divide-y divide-gray-200"></tbody>
                        </table>
                    </div>
                </div>
                <div class="flex justify-end space-x-2 mt-6">
                    <button id="exportFinalResultPdfBtn" class="bg-red-600 text-white font-semibold py-2 px-4 rounded-lg hover:bg-red-700 flex items-center gap-2"><i data-lucide="file-text" class="w-4 h-4"></i>Export PDF</button>
                    <button id="closeFinalResultModalBtn" class="bg-gray-300 text-gray-800 font-semibold py-2 px-4 rounded-lg hover:bg-gray-400">Band Karein</button>
                </div>`;
                
                lucide.createIcons();
                renderFinalResults('All');

                container.querySelector('#finalResultFilters').addEventListener('click', (e) => {
                    if (e.target.matches('.filter-btn')) {
                        const filter = e.target.dataset.filter;
                        renderFinalResults(filter);
                        container.querySelectorAll('.filter-btn').forEach(btn => btn.classList.remove('bg-blue-600', 'text-white'));
                        e.target.classList.add('bg-blue-600', 'text-white');
                    }
                });

                document.getElementById('closeFinalResultModalBtn').onclick = () => hideModal('finalResultModal');
                document.getElementById('exportFinalResultPdfBtn').onclick = () => exportToPdf(document.getElementById('printableFinalResults'), 'final_results.pdf');
                showModal('finalResultModal');
            });

            // --- Export Functions ---
            const exportToPdf = (element, filename, orientation = 'landscape') => {
                if (!element) return;
                element.classList.add('printable-background');
                html2canvas(element, { scale: 2, useCORS: true, logging: false }).then(canvas => {
                    element.classList.remove('printable-background');
                    const imgData = canvas.toDataURL('image/png');
                    const imgWidth = canvas.width;
                    const imgHeight = canvas.height;

                    const pdf = new jsPDF({ orientation: orientation, unit: 'pt', format: 'a4' });
                    const pdfWidth = pdf.internal.pageSize.getWidth();
                    const pdfHeight = pdf.internal.pageSize.getHeight();

                    const ratio = Math.min(pdfWidth / imgWidth, pdfHeight / imgHeight);
                    const newImgWidth = imgWidth * ratio;
                    const newImgHeight = imgHeight * ratio;

                    const x = (pdfWidth - newImgWidth) / 2;
                    const y = (pdfHeight - newImgHeight) / 2;

                    pdf.addImage(imgData, 'PNG', x, y, newImgWidth, newImgHeight);
                    pdf.save(filename);
                }).catch(err => {
                    console.error("PDF generation error:", err);
                    element.classList.remove('printable-background');
                });
            };

            const exportToExcel = () => {
                if (currentResults.length === 0) return;
                const headers = ['Student Name', 'Class', 'Subject', 'Test Type', 'Topic', 'Score', 'Total Marks', 'Percentage', 'Grade', 'Date'];
                const rows = currentResults.map(res => {
                    const percentage = res.totalMarks > 0 ? ((res.score / res.totalMarks) * 100).toFixed(2) : 'N/A';
                    const values = [ res.studentName, res.studentClass, res.subject, res.testType, res.topicName, res.score, res.totalMarks, `${percentage}%`, calculateGrade(percentage), new Date(res.resultDate).toLocaleDateString() ];
                    return values.map(val => `"${String(val).replace(/"/g, '""')}"`).join(',');
                });
                const csvContent = "data:text/csv;charset=utf-8," + headers.join(',') + '\n' + rows.join('\n');
                const link = document.createElement("a");
                link.setAttribute("href", encodeURI(csvContent));
                link.setAttribute("download", "student_results.csv");
                document.body.appendChild(link);
                link.click();
                document.body.removeChild(link);
            };
            document.getElementById('exportPdfBtn').addEventListener('click', () => exportToPdf(document.getElementById('resultsTable'), 'all_results.pdf'));
            document.getElementById('exportExcelBtn').addEventListener('click', exportToExcel);

            // --- Search ---
            searchInput.addEventListener('input', (e) => {
                const searchTerm = e.target.value.toLowerCase();
                const rows = resultsTableBody.getElementsByTagName('tr');
                let resultsFound = false;
                for (let row of rows) {
                    const isVisible = row.dataset.studentName.includes(searchTerm);
                    row.style.display = isVisible ? '' : 'none';
                    if (isVisible) resultsFound = true;
                }
                const hasResults = currentResults.length > 0;
                noResultsMessage.classList.toggle('hidden', searchTerm ? resultsFound : hasResults);
                if (!resultsFound && searchTerm) noResultsMessage.innerHTML = `<p>No results found for "${e.target.value}".</p>`;
                else if (hasResults === false) noResultsMessage.innerHTML = `<p>Abhi tak koi result upload nahi hua hai.</p>`;
            });

            // --- Close Modals on Overlay Click ---
            document.querySelectorAll('.modal-overlay').forEach(modal => {
                modal.addEventListener('click', (e) => {
                    if (e.target === modal) hideModal(modal.id);
                });
            });

            // --- Initial App Load ---
            const initializeApp = () => {
                loadResultsFromLocal();
                loadSubjectsFromLocal();
                renderResults();
                populateSubjectDropdowns();
            };
            
            initializeApp();
        });
    </script>
</body>
</html>

