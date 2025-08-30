<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Student Result Manager</title>
    <!-- Tailwind CSS CDN -->
    <script src="https://cdn.tailwindcss.com"></script>
    <!-- Google Fonts: Inter -->
    <link rel="preconnect" href="https://fonts.googleapis.com">
    <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600;700&display=swap" rel="stylesheet">
    <!-- PDF Generation Libraries -->
    <script src="https://cdnjs.cloudflare.com/ajax/libs/html2canvas/1.4.1/html2canvas.min.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/jspdf/2.5.1/jspdf.umd.min.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/jspdf-autotable/3.5.23/jspdf.plugin.autotable.min.js"></script>
    <!-- Excel Library -->
    <script src="https://cdn.jsdelivr.net/npm/xlsx@0.17.0/dist/xlsx.full.min.js"></script>
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
                    <h1 class="text-2xl font-bold text-gray-800">Student Result Management System</h1>
                    <p class="text-sm text-gray-500">For IBAGRADS XI-XII</p>
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
                        <label for="topicName" class="block text-sm font-medium text-gray-600 mb-1">Topic ka Naam</label>
                        <input type="text" id="topicName" placeholder="Jaise: Algebra" class="w-full px-4 py-2 bg-white border border-gray-300 rounded-lg focus:ring-2 focus:ring-blue-500 transition" required>
                    </div>
                    <div>
                        <label for="testType" class="block text-sm font-medium text-gray-600 mb-1">Test Type</label>
                        <select id="testType" class="w-full px-4 py-2 bg-white border border-gray-300 rounded-lg focus:ring-2 focus:ring-blue-500 transition" required>
                            <option value="" disabled selected>Select Test Type</option>
                            <option value="Weekly Test">Weekly Test</option>
                            <option value="Monthly Test">Monthly Test</option>
                            <option value="Yearly Test">Yearly Test</option>
                        </select>
                    </div>
                    <div>
                        <label for="score" class="block text-sm font-medium text-gray-600 mb-1">Score</label>
                        <input type="number" id="score" placeholder="Jaise: 85" min="0" class="w-full px-4 py-2 bg-white border border-gray-300 rounded-lg focus:ring-2 focus:ring-blue-500 transition" required>
                    </div>
                    <div>
                        <label for="totalMarks" class="block text-sm font-medium text-gray-600 mb-1">Total Marks</label>
                        <input type="number" id="totalMarks" value="100" min="1" class="w-full px-4 py-2 bg-white border border-gray-300 rounded-lg focus:ring-2 focus:ring-blue-500 transition" required>
                    </div>
                    <div class="md:col-span-2">
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

            <!-- New Report Buttons -->
            <div class="flex flex-wrap items-center justify-center sm:justify-end gap-2 mb-4">
                <button id="weeklyReportBtn" class="bg-blue-600 text-white font-semibold py-2 px-4 rounded-lg hover:bg-blue-700 transition flex items-center gap-2">Weekly Test Report</button>
                <button id="monthlyReportBtn" class="bg-yellow-600 text-white font-semibold py-2 px-4 rounded-lg hover:bg-yellow-700 transition flex items-center gap-2">Monthly Test Report</button>
                <button id="yearlyReportBtn" class="bg-purple-600 text-white font-semibold py-2 px-4 rounded-lg hover:bg-purple-700 transition flex items-center gap-2">Yearly Test Report</button>
            </div>
            <!-- Message box for reports -->
            <div id="reportMessageBox" class="hidden text-center p-4 rounded-lg bg-blue-50 text-blue-800 mb-4 transition-all duration-300 ease-in-out"></div>
            
            <table id="resultsTable" class="min-w-full bg-white rounded-lg shadow-md overflow-hidden">
                <thead class="bg-gray-100 text-gray-600">
                    <tr>
                        <th class="text-left py-3 px-4 uppercase font-semibold text-sm">Student Naam</th>
                        <th class="text-left py-3 px-4 uppercase font-semibold text-sm">Class</th>
                        <th class="text-left py-3 px-4 uppercase font-semibold text-sm">Subject</th>
                        <th class="text-left py-3 px-4 uppercase font-semibold text-sm">Topic</th>
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
    <div id="reportModal" class="modal-overlay">
        <div id="reportModalContainer" class="modal-container w-full max-w-5xl"></div>
    </div>
    
    <script>
        document.addEventListener('DOMContentLoaded', () => {
            lucide.createIcons();
            
            // --- Global Variables & Constants ---
            const { jsPDF } = window.jspdf;
            document.getElementById('currentDate').textContent = new Date().toLocaleDateString('en-GB', { day: 'numeric', month: 'long', year: 'numeric' });

            const resultForm = document.getElementById('resultForm');
            const resultsTableBody = document.getElementById('resultsTableBody');
            const noResultsMessage = document.getElementById('noResultsMessage');
            const searchInput = document.getElementById('searchInput'); 
            const subjectSelect = document.getElementById('subject');
            const newSubjectInput = document.getElementById('newSubjectInput');
            const addSubjectBtn = document.getElementById('addSubjectBtn');
            const removeSubjectBtn = document.getElementById('removeSubjectBtn');
            const subjectError = document.getElementById('subjectError');
            
            const weeklyReportBtn = document.getElementById('weeklyReportBtn');
            const monthlyReportBtn = document.getElementById('monthlyReportBtn');
            const yearlyReportBtn = document.getElementById('yearlyReportBtn');
            const reportMessageBox = document.getElementById('reportMessageBox');
            const exportPdfBtn = document.getElementById('exportPdfBtn');
            const exportExcelBtn = document.getElementById('exportExcelBtn');
            const showFinalResultBtn = document.getElementById('showFinalResultBtn');

            let currentResults = [];
            let subjects = [];

            // --- Data Persistence ---
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
                        <td class="py-3 px-4">${data.topicName}</td>
                        <td class="py-3 px-4">${data.testType}</td>
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

            // --- Subject Management ---
            addSubjectBtn.addEventListener('click', () => {
                const newSubject = newSubjectInput.value.trim();
                if (!newSubject) {
                    subjectError.textContent = "Please enter a subject name.";
                    subjectError.classList.remove('hidden');
                    return;
                }
                if (subjects.find(s => s.toLowerCase() === newSubject.toLowerCase())) {
                     subjectError.textContent = "Subject already exists.";
                     subjectError.classList.remove('hidden');
                     return;
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
                    subjectError.classList.remove('hidden');
                    return;
                }
                subjects = subjects.filter(s => s !== selected);
                saveSubjectsToLocal();
                populateSubjectDropdowns();
                subjectError.classList.add('hidden');
            });
            
            // --- Add/Edit Result Forms ---
            resultForm.addEventListener('submit', (e) => {
                e.preventDefault();
                const newResult = {
                    id: crypto.randomUUID(),
                    studentName: document.getElementById('studentName').value.trim(),
                    studentClass: document.getElementById('studentClass').value,
                    subject: subjectSelect.value,
                    topicName: document.getElementById('topicName').value.trim(),
                    testType: document.getElementById('testType').value,
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
                document.getElementById('testType').selectedIndex = 0;
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
                            <td class="border p-2">${data.topicName}</td>
                            <td class="border p-2">${data.testType}</td>
                            <td class="border p-2 text-center">${data.score}</td>
                            <td class="border p-2 text-center">${data.totalMarks}</td>
                            <td class="border p-2 text-center font-semibold">${percentage}%</td>
                        </tr>`;
                }).join('');

                return `
                <div class="p-8 border-2 border-gray-800 bg-white relative">
                    <div class="absolute inset-0 flex items-center justify-center z-0">
                        <p class="text-gray-200 text-8xl font-black select-none opacity-50">IBAGARDS</p>
                    </div>
                    <div class="relative z-10">
                        <div class="flex items-center justify-between pb-4 border-b-2 border-gray-800">
                            <div class="flex items-center gap-3">
                                <svg xmlns="http://www.w3.org/2000/svg" width="40" height="40" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="text-blue-600"><path d="M4 19.5v-15A2.5 2.5 0 0 1 6.5 2H20v20H6.5a2.5 2.5 0 0 1 0-5H20"></path></svg>
                                <div>
                                    <h3 class="text-xl font-bold text-gray-800">IBAGARDS XI-XII</h3>
                                    <p class="text-sm font-medium text-gray-500">OFFICIAL RESULT CARD</p>
                                </div>
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
                                    <th class="border p-2 text-left">Topic</th>
                                    <th class="border p-2 text-left">Test Type</th>
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
                            <p>This is a computer-generated document. | IBAGARDS XI-XII, Karachi, Sindh, Pakistan</p>
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
                document.getElementById('exportCardPdfBtn').onclick = () => {
                    const element = document.getElementById('printableCard');
                    const filename = `${results[0].studentName}_result.pdf`;
                    exportHtmlToPdf(element, filename);
                };
                showModal('cardModal');
            };
            
            // New PDF export function for full table
            const exportTableToPdf = () => {
                const doc = new jsPDF('p', 'mm', 'a4');
                const table = document.getElementById('resultsTable');
                
                doc.autoTable({
                    html: '#resultsTable',
                    startY: 20,
                    headStyles: { fillColor: [243, 244, 246], textColor: [75, 85, 99], fontStyle: 'bold' },
                    bodyStyles: { textColor: [55, 65, 81] },
                    alternateRowStyles: { fillColor: [255, 255, 255] }
                });

                doc.save('student_results.pdf');
            };

            // New Excel export function for full table
            const exportTableToExcel = () => {
                const data = currentResults.map(r => ({
                    'Student Name': r.studentName,
                    'Class': r.studentClass,
                    'Subject': r.subject,
                    'Topic Name': r.topicName,
                    'Test Type': r.testType,
                    'Score': r.score,
                    'Total Marks': r.totalMarks,
                    'Percentage': ((r.score / r.totalMarks) * 100).toFixed(2) + '%',
                    'Date': r.resultDate
                }));

                const worksheet = XLSX.utils.json_to_sheet(data);
                const workbook = { Sheets: { 'data': worksheet }, SheetNames: ['data'] };
                XLSX.writeFile(workbook, 'student_results.xlsx');
            };

            // New function to handle HTML to PDF export with scaling
            const exportHtmlToPdf = (element, filename) => {
                const pdf = new jsPDF('p', 'mm', 'a4');
                const margin = 10;
                const pdfWidth = pdf.internal.pageSize.getWidth() - 2 * margin;

                html2canvas(element, { scale: 2 }).then(canvas => {
                    const imgData = canvas.toDataURL('image/png');
                    const imgProps = pdf.getImageProperties(imgData);
                    const imgHeight = (imgProps.height * pdfWidth) / imgProps.width;

                    let heightLeft = imgHeight;
                    let position = 0;
                    
                    pdf.addImage(imgData, 'PNG', margin, position + margin, pdfWidth, imgHeight);
                    heightLeft -= pdf.internal.pageSize.getHeight();

                    while (heightLeft >= 0) {
                        position = heightLeft - imgHeight;
                        pdf.addPage();
                        pdf.addImage(imgData, 'PNG', margin, position + margin, pdfWidth, imgHeight);
                        heightLeft -= pdf.internal.pageSize.getHeight();
                    }
                    
                    pdf.save(filename);
                });
            };

            // --- Report Generation Logic (New) ---
            const generateSpecificReport = (testType) => {
                const filteredResults = currentResults.filter(r => r.testType === testType);
                const container = document.getElementById('reportModalContainer');
                
                let htmlContent = `
                    <div class="flex justify-between items-center mb-6 border-b pb-4">
                        <h2 class="text-2xl font-bold text-gray-800">${testType} Summary</h2>
                        <button id="closeReportModalBtn" class="bg-gray-300 text-gray-800 font-semibold py-1.5 px-3 rounded-lg hover:bg-gray-400 transition">Band Karein</button>
                    </div>
                `;
                
                if (filteredResults.length === 0) {
                    htmlContent += `<p class="text-center text-gray-500">Is category mein koi bhi result nahi mila.</p>`;
                } else {
                    const uniqueStudents = [...new Set(filteredResults.map(r => r.studentName))];
                    const summaryData = uniqueStudents.map(studentName => {
                        const studentTests = filteredResults.filter(r => r.studentName === studentName);
                        const totalScore = studentTests.reduce((sum, r) => sum + r.score, 0);
                        const totalMarks = studentTests.reduce((sum, r) => sum + r.totalMarks, 0);
                        const percentage = totalMarks > 0 ? ((totalScore / totalMarks) * 100).toFixed(2) : 0;
                        return { studentName, totalTests: studentTests.length, totalScore, totalMarks, percentage };
                    });

                    const tableRows = summaryData.map(data => `
                        <tr class="hover:bg-gray-50 transition">
                            <td class="py-3 px-4">${data.studentName}</td>
                            <td class="py-3 px-4">${data.totalTests}</td>
                            <td class="py-3 px-4">${data.totalScore} / ${data.totalMarks}</td>
                            <td class="py-3 px-4 font-semibold text-blue-600">${data.percentage}%</td>
                        </tr>
                    `).join('');

                    htmlContent += `
                        <div class="overflow-x-auto">
                            <table class="min-w-full bg-white rounded-lg shadow-md overflow-hidden">
                                <thead class="bg-gray-100 text-gray-600">
                                    <tr>
                                        <th class="text-left py-3 px-4 uppercase font-semibold text-sm">Student Naam</th>
                                        <th class="text-left py-3 px-4 uppercase font-semibold text-sm">Test Count</th>
                                        <th class="text-left py-3 px-4 uppercase font-semibold text-sm">Total Score</th>
                                        <th class="text-left py-3 px-4 uppercase font-semibold text-sm">Average Percentage</th>
                                    </tr>
                                </thead>
                                <tbody class="text-gray-700 divide-y divide-gray-200">
                                    ${tableRows}
                                </tbody>
                            </table>
                        </div>
                    `;
                }

                container.innerHTML = htmlContent;
                document.getElementById('closeReportModalBtn').onclick = () => hideModal('reportModal');
                showModal('reportModal');
            };

            // --- Final Result Logic (Updated) ---
            const showFinalResultsModal = () => {
                const container = document.getElementById('finalResultModalContainer');
                const classes = ['XI', 'XII'];
                
                let htmlContent = `
                    <h2 class="text-2xl font-bold text-gray-800 mb-6 text-center">Sabhi Students ka Final Result Summary</h2>
                `;
                
                classes.forEach(studentClass => {
                    const classResults = currentResults.filter(r => r.studentClass === studentClass);
                    const uniqueStudentsInClass = [...new Set(classResults.map(r => r.studentName))];
                    
                    if (uniqueStudentsInClass.length > 0) {
                        htmlContent += `
                            <h3 class="text-xl font-semibold text-gray-700 mt-8 mb-4">Class ${studentClass}</h3>
                            <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-6">
                        `;

                        uniqueStudentsInClass.forEach(studentName => {
                            const studentTests = classResults.filter(r => r.studentName === studentName);
                            const totalScore = studentTests.reduce((sum, r) => sum + r.score, 0);
                            const totalMarks = studentTests.reduce((sum, r) => sum + r.totalMarks, 0);
                            const percentage = totalMarks > 0 ? ((totalScore / totalMarks) * 100).toFixed(2) : 0;
                            const grade = calculateGrade(percentage);

                            htmlContent += `
                                <div class="bg-gray-50 p-6 rounded-xl shadow-sm border border-gray-200">
                                    <h4 class="text-lg font-bold text-gray-700 mb-2">${studentName}</h4>
                                    <p class="text-sm">Tests Taken: <span class="font-semibold">${studentTests.length}</span></p>
                                    <p class="text-sm">Total Percentage: <span class="font-semibold">${percentage}%</span></p>
                                    <p class="text-sm">Final Grade: <span class="font-semibold">${grade}</span></p>
                                </div>
                            `;
                        });
                        htmlContent += `</div>`;
                    }
                });

                if (currentResults.length === 0) {
                    htmlContent += `<p class="col-span-full text-center text-gray-500">Koi bhi final result abhi tak upload nahi hua hai.</p>`;
                }

                htmlContent += `
                    <div class="flex justify-center mt-6">
                        <button id="closeFinalResultModalBtn" class="bg-gray-300 text-gray-800 font-semibold py-2 px-4 rounded-lg hover:bg-gray-400 transition">Band Karein</button>
                    </div>
                `;

                container.innerHTML = htmlContent;
                document.getElementById('closeFinalResultModalBtn').onclick = () => hideModal('finalResultModal');
                showModal('finalResultModal');
            };

            // --- Event Listeners ---
            weeklyReportBtn.addEventListener('click', () => generateSpecificReport('Weekly Test'));
            monthlyReportBtn.addEventListener('click', () => generateSpecificReport('Monthly Test'));
            yearlyReportBtn.addEventListener('click', () => generateSpecificReport('Yearly Test'));
            exportPdfBtn.addEventListener('click', exportTableToPdf);
            exportExcelBtn.addEventListener('click', exportTableToExcel);
            showFinalResultBtn.addEventListener('click', showFinalResultsModal);

            // --- Initialization ---
            loadSubjectsFromLocal();
            loadResultsFromLocal();
            populateSubjectDropdowns();
            renderResults();
        });
    </script>
</body>
</html>
