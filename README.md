# Assignment-2
An employee dashboard created using react library with navbar having home and employee form links
import React, { useState, useEffect } from 'react';
import { 
  Users, 
  UserPlus, 
  Search, 
  RefreshCw, 
  Building, 
  Mail, 
  Briefcase, 
  MapPin, 
  DollarSign, 
  CheckCircle, 
  AlertCircle,
  LayoutDashboard,
  X,
  Menu
} from 'lucide-react';

export default function App() {
  // Navigation State: 'dashboard' | 'form'
  const [activeTab, setActiveTab] = useState('dashboard');
  const [mobileMenuOpen, setMobileMenuOpen] = useState(false);

  // Employee Data State
  const [employees, setEmployees] = useState([]);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);
  const [searchTerm, setSearchTerm] = useState('');
  const [viewMode, setViewMode] = useState('cards'); // 'cards' | 'table'

  // Form State
  const [formData, setFormData] = useState({
    name: '',
    designation: '',
    location: '',
    salary: ''
  });
  const [formErrors, setFormErrors] = useState({});
  const [toastMessage, setToastMessage] = useState(null);

  // Fetch initial employee data from external API
  const fetchEmployees = async () => {
    setLoading(true);
    setError(null);
    try {
      const response = await fetch('https://jsonplaceholder.typicode.com/users');
      if (!response.ok) {
        throw new Error(`Failed to fetch data (Status: ${response.status})`);
      }
      const data = await response.json();
      setEmployees(data);
    } catch (err) {
      setError(err.message || 'An error occurred while fetching employee data.');
    } finally {
      setLoading(false);
    }
  };

  useEffect(() => {
    fetchEmployees();
  }, []);

  // Handle Form Validation
  const validateForm = () => {
    const errors = {};
    if (!formData.name.trim()) errors.name = 'Full Name is required';
    if (!formData.designation.trim()) errors.designation = 'Designation is required';
    if (!formData.location.trim()) errors.location = 'Location is required';
    
    if (!formData.salary) {
      errors.salary = 'Salary is required';
    } else if (isNaN(formData.salary) || Number(formData.salary) <= 0) {
      errors.salary = 'Salary must be a positive number';
    }

    setFormErrors(errors);
    return Object.keys(errors).length === 0;
  };

  // Handle Form Submit
  const handleSubmit = (e) => {
    e.preventDefault();
    if (validateForm()) {
      // Trigger success notification
      showToast(`Employee "${formData.name}" details recorded successfully!`);
      
      // Reset form
      handleReset();
    }
  };

  // Reset Form Fields
  const handleReset = () => {
    setFormData({
      name: '',
      designation: '',
      location: '',
      salary: ''
    });
    setFormErrors({});
  };

  // Display temporary Toast message
  const showToast = (msg) => {
    setToastMessage(msg);
    setTimeout(() => {
      setToastMessage(null);
    }, 4000);
  };

  // Filter employees based on search query
  const filteredEmployees = employees.filter(emp => 
    emp.name.toLowerCase().includes(searchTerm.toLowerCase()) ||
    emp.email.toLowerCase().includes(searchTerm.toLowerCase()) ||
    emp.username.toLowerCase().includes(searchTerm.toLowerCase())
  );

  return (
    <div className="min-h-screen bg-slate-50 text-slate-800 flex flex-col font-sans">
      
      {/* Toast Notification */}
      {toastMessage && (
        <div className="fixed top-20 right-4 z-50 flex items-center gap-3 bg-emerald-600 text-white px-5 py-3 rounded-lg shadow-xl border border-emerald-500 animate-bounce transition-all">
          <CheckCircle className="w-5 h-5 flex-shrink-0" />
          <span className="text-sm font-medium">{toastMessage}</span>
          <button 
            onClick={() => setToastMessage(null)}
            className="ml-auto hover:bg-emerald-700 p-1 rounded-full transition-colors"
          >
            <X className="w-4 h-4" />
          </button>
        </div>
      )}

      {/* Navigation Bar */}
      <nav className="bg-slate-900 text-white shadow-md sticky top-0 z-40">
        <div className="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8">
          <div className="flex items-center justify-between h-16">
            
            {/* Branding Logo */}
            <div className="flex items-center gap-3 cursor-pointer" onClick={() => setActiveTab('dashboard')}>
              <div className="bg-indigo-600 p-2 rounded-lg">
                <LayoutDashboard className="w-6 h-6 text-white" />
              </div>
              <span className="font-bold text-xl tracking-tight bg-gradient-to-r from-white to-slate-300 bg-clip-text text-transparent">
                WorkPulse HQ
              </span>
            </div>

            {/* Desktop Navigation */}
            <div className="hidden md:flex items-center space-x-2">
              <button
                onClick={() => setActiveTab('dashboard')}
                className={`flex items-center gap-2 px-4 py-2 rounded-lg text-sm font-medium transition-colors ${
                  activeTab === 'dashboard'
                    ? 'bg-indigo-600 text-white shadow-sm'
                    : 'text-slate-300 hover:bg-slate-800 hover:text-white'
                }`}
              >
                <Users className="w-4 h-4" />
                Home / Dashboard
              </button>

              <button
                onClick={() => setActiveTab('form')}
                className={`flex items-center gap-2 px-4 py-2 rounded-lg text-sm font-medium transition-colors ${
                  activeTab === 'form'
                    ? 'bg-indigo-600 text-white shadow-sm'
                    : 'text-slate-300 hover:bg-slate-800 hover:text-white'
                }`}
              >
                <UserPlus className="w-4 h-4" />
                Employee Form
              </button>
            </div>

            {/* Mobile Menu Button */}
            <div className="md:hidden flex items-center">
              <button
                onClick={() => setMobileMenuOpen(!mobileMenuOpen)}
                className="p-2 rounded-md text-slate-300 hover:text-white hover:bg-slate-800 focus:outline-none"
              >
                {mobileMenuOpen ? <X className="w-6 h-6" /> : <Menu className="w-6 h-6" />}
              </button>
            </div>
          </div>
        </div>

        {/* Mobile Navigation Menu */}
        {mobileMenuOpen && (
          <div className="md:hidden bg-slate-800 px-2 pt-2 pb-3 space-y-1 sm:px-3 border-t border-slate-700">
            <button
              onClick={() => {
                setActiveTab('dashboard');
                setMobileMenuOpen(false);
              }}
              className={`w-full flex items-center gap-3 px-3 py-2 rounded-md text-base font-medium ${
                activeTab === 'dashboard' ? 'bg-indigo-600 text-white' : 'text-slate-300 hover:bg-slate-700'
              }`}
            >
              <Users className="w-5 h-5" />
              Home / Dashboard
            </button>
            <button
              onClick={() => {
                setActiveTab('form');
                setMobileMenuOpen(false);
              }}
              className={`w-full flex items-center gap-3 px-3 py-2 rounded-md text-base font-medium ${
                activeTab === 'form' ? 'bg-indigo-600 text-white' : 'text-slate-300 hover:bg-slate-700'
              }`}
            >
              <UserPlus className="w-5 h-5" />
              Employee Form
            </button>
          </div>
        )}
      </nav>

      {/* Main Body Area */}
      <main className="flex-1 max-w-7xl w-full mx-auto px-4 sm:px-6 lg:px-8 py-8">
        
        {/* ================= DASHBOARD TAB ================= */}
        {activeTab === 'dashboard' && (
          <div className="space-y-6">
            
            {/* Header Controls */}
            <div className="flex flex-col md:flex-row md:items-center justify-between gap-4 bg-white p-6 rounded-xl shadow-sm border border-slate-200">
              <div>
                <h1 className="text-2xl font-bold text-slate-900">Employee Directory</h1>
                <p className="text-sm text-slate-500 mt-1">Manage and view company staff records from API source</p>
              </div>

              <div className="flex flex-wrap items-center gap-3">
                {/* Search Bar */}
                <div className="relative flex-1 sm:w-64">
                  <Search className="w-4 h-4 absolute left-3 top-1/2 -translate-y-1/2 text-slate-400" />
                  <input
                    type="text"
                    placeholder="Search name or email..."
                    value={searchTerm}
                    onChange={(e) => setSearchTerm(e.target.value)}
                    className="w-full pl-9 pr-4 py-2 text-sm bg-slate-50 border border-slate-300 rounded-lg focus:outline-none focus:ring-2 focus:ring-indigo-500 focus:bg-white transition-all"
                  />
                </div>

                {/* View Switcher Toggle */}
                <div className="flex bg-slate-100 p-1 rounded-lg border border-slate-200">
                  <button
                    onClick={() => setViewMode('cards')}
                    className={`px-3 py-1.5 text-xs font-semibold rounded-md transition-all ${
                      viewMode === 'cards' ? 'bg-white text-indigo-600 shadow-sm' : 'text-slate-600 hover:text-slate-900'
                    }`}
                  >
                    Cards
                  </button>
                  <button
                    onClick={() => setViewMode('table')}
                    className={`px-3 py-1.5 text-xs font-semibold rounded-md transition-all ${
                      viewMode === 'table' ? 'bg-white text-indigo-600 shadow-sm' : 'text-slate-600 hover:text-slate-900'
                    }`}
                  >
                    Table
                  </button>
                </div>

                {/* Refresh Data Button */}
                <button
                  onClick={fetchEmployees}
                  className="flex items-center gap-1.5 px-3 py-2 text-sm bg-slate-100 hover:bg-slate-200 text-slate-700 font-medium rounded-lg border border-slate-200 transition-colors"
                  title="Reload data from API"
                >
                  <RefreshCw className={`w-4 h-4 ${loading ? 'animate-spin' : ''}`} />
                  <span className="hidden sm:inline">Refresh</span>
                </button>
              </div>
            </div>

            {/* Loading Indicator */}
            {loading && (
              <div className="flex flex-col items-center justify-center py-16 bg-white rounded-xl shadow-sm border border-slate-200">
                <RefreshCw className="w-10 h-10 text-indigo-600 animate-spin mb-4" />
                <p className="text-slate-600 font-medium">Fetching employee profiles...</p>
              </div>
            )}

            {/* Error Message Box */}
            {error && !loading && (
              <div className="bg-rose-50 border border-rose-200 rounded-xl p-6 text-center text-rose-700">
                <AlertCircle className="w-10 h-10 mx-auto text-rose-500 mb-3" />
                <h3 className="font-semibold text-lg">Failed to Load Employees</h3>
                <p className="text-sm text-rose-600 mt-1">{error}</p>
                <button
                  onClick={fetchEmployees}
                  className="mt-4 px-4 py-2 bg-rose-600 text-white rounded-lg text-sm font-medium hover:bg-rose-700 transition-colors"
                >
                  Try Again
                </button>
              </div>
            )}

            {/* Content View: Cards View */}
            {!loading && !error && viewMode === 'cards' && (
              <div className="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-6">
                {filteredEmployees.map((emp) => (
                  <div
                    key={emp.id}
                    className="bg-white rounded-xl border border-slate-200 p-6 shadow-sm hover:shadow-md transition-shadow relative flex flex-col justify-between"
                  >
                    <div>
                      {/* Header Badge */}
                      <div className="flex items-center justify-between mb-4">
                        <span className="text-xs font-bold text-indigo-600 bg-indigo-50 px-2.5 py-1 rounded-full border border-indigo-100">
                          ID: #{emp.id}
                        </span>
                        <span className="text-xs font-medium text-slate-400">
                          @{emp.username}
                        </span>
                      </div>

                      {/* Employee Title */}
                      <h3 className="text-lg font-bold text-slate-900 group-hover:text-indigo-600 transition-colors">
                        {emp.name}
                      </h3>

                      {/* Details list */}
                      <div className="mt-4 space-y-2 text-sm text-slate-600">
                        <div className="flex items-center gap-2.5">
                          <Mail className="w-4 h-4 text-slate-400 flex-shrink-0" />
                          <a href={`mailto:${emp.email}`} className="hover:text-indigo-600 truncate">
                            {emp.email}
                          </a>
                        </div>
                        <div className="flex items-center gap-2.5">
                          <Building className="w-4 h-4 text-slate-400 flex-shrink-0" />
                          <span className="truncate">{emp.company?.name || 'N/A'}</span>
                        </div>
                      </div>
                    </div>

                    <div className="mt-6 pt-4 border-t border-slate-100 flex items-center justify-between text-xs text-slate-500">
                      <span>City: {emp.address?.city || 'N/A'}</span>
                      <span className="inline-block w-2 h-2 rounded-full bg-emerald-500" title="Active Status"></span>
                    </div>
                  </div>
                ))}

                {filteredEmployees.length === 0 && (
                  <div className="col-span-full py-12 text-center bg-white rounded-xl border border-slate-200">
                    <p className="text-slate-500">No employees match your search criteria.</p>
                  </div>
                )}
              </div>
            )}

            {/* Content View: Table View */}
            {!loading && !error && viewMode === 'table' && (
              <div className="bg-white rounded-xl shadow-sm border border-slate-200 overflow-hidden">
                <div className="overflow-x-auto">
                  <table className="w-full text-left border-collapse">
                    <thead>
                      <tr className="bg-slate-50 border-b border-slate-200 text-xs font-semibold text-slate-500 uppercase tracking-wider">
                        <th className="py-3.5 px-4">ID</th>
                        <th className="py-3.5 px-4">Name</th>
                        <th className="py-3.5 px-4">Username</th>
                        <th className="py-3.5 px-4">Email</th>
                        <th className="py-3.5 px-4">Company</th>
                      </tr>
                    </thead>
                    <tbody className="divide-y divide-slate-200 text-sm">
                      {filteredEmployees.map((emp) => (
                        <tr key={emp.id} className="hover:bg-slate-50 transition-colors">
                          <td className="py-3.5 px-4 font-mono text-slate-500 font-medium">#{emp.id}</td>
                          <td className="py-3.5 px-4 font-semibold text-slate-900">{emp.name}</td>
                          <td className="py-3.5 px-4 text-slate-500">@{emp.username}</td>
                          <td className="py-3.5 px-4 text-indigo-600">{emp.email}</td>
                          <td className="py-3.5 px-4 text-slate-600">{emp.company?.name || 'N/A'}</td>
                        </tr>
                      ))}

                      {filteredEmployees.length === 0 && (
                        <tr>
                          <td colSpan="5" className="py-8 text-center text-slate-500">
                            No employees found.
                          </td>
                        </tr>
                      )}
                    </tbody>
                  </table>
                </div>
              </div>
            )}

          </div>
        )}

        {/* ================= EMPLOYEE FORM TAB ================= */}
        {activeTab === 'form' && (
          <div className="max-w-2xl mx-auto">
            <div className="bg-white rounded-xl shadow-sm border border-slate-200 overflow-hidden">
              
              {/* Form Header */}
              <div className="bg-slate-900 text-white p-6">
                <h2 className="text-xl font-bold flex items-center gap-2">
                  <UserPlus className="w-5 h-5 text-indigo-400" />
                  Add New Employee
                </h2>
                <p className="text-sm text-slate-400 mt-1">
                  Fill out the form below to add employee details to local state.
                </p>
              </div>

              {/* Form Inputs */}
              <form onSubmit={handleSubmit} className="p-6 space-y-5" noValidate>
                
                {/* Full Name Field */}
                <div>
                  <label className="block text-sm font-semibold text-slate-700 mb-1">
                    Full Name <span className="text-rose-500">*</span>
                  </label>
                  <div className="relative">
                    <Users className="w-5 h-5 text-slate-400 absolute left-3 top-1/2 -translate-y-1/2" />
                    <input
                      type="text"
                      placeholder="e.g. Jane Doe"
                      value={formData.name}
                      onChange={(e) => setFormData({ ...formData, name: e.target.value })}
                      className={`w-full pl-10 pr-4 py-2.5 text-sm border rounded-lg focus:outline-none focus:ring-2 transition-all ${
                        formErrors.name
                          ? 'border-rose-400 focus:ring-rose-500 bg-rose-50/30'
                          : 'border-slate-300 focus:ring-indigo-500 focus:border-indigo-500'
                      }`}
                    />
                  </div>
                  {formErrors.name && (
                    <p className="text-xs text-rose-500 mt-1">{formErrors.name}</p>
                  )}
                </div>

                {/* Designation Field */}
                <div>
                  <label className="block text-sm font-semibold text-slate-700 mb-1">
                    Designation <span className="text-rose-500">*</span>
                  </label>
                  <div className="relative">
                    <Briefcase className="w-5 h-5 text-slate-400 absolute left-3 top-1/2 -translate-y-1/2" />
                    <input
                      type="text"
                      placeholder="e.g. Senior Frontend Developer"
                      value={formData.designation}
                      onChange={(e) => setFormData({ ...formData, designation: e.target.value })}
                      className={`w-full pl-10 pr-4 py-2.5 text-sm border rounded-lg focus:outline-none focus:ring-2 transition-all ${
                        formErrors.designation
                          ? 'border-rose-400 focus:ring-rose-500 bg-rose-50/30'
                          : 'border-slate-300 focus:ring-indigo-500 focus:border-indigo-500'
                      }`}
                    />
                  </div>
                  {formErrors.designation && (
                    <p className="text-xs text-rose-500 mt-1">{formErrors.designation}</p>
                  )}
                </div>

                {/* Location Field */}
                <div>
                  <label className="block text-sm font-semibold text-slate-700 mb-1">
                    Location <span className="text-rose-500">*</span>
                  </label>
                  <div className="relative">
                    <MapPin className="w-5 h-5 text-slate-400 absolute left-3 top-1/2 -translate-y-1/2" />
                    <input
                      type="text"
                      placeholder="e.g. New York, USA"
                      value={formData.location}
                      onChange={(e) => setFormData({ ...formData, location: e.target.value })}
                      className={`w-full pl-10 pr-4 py-2.5 text-sm border rounded-lg focus:outline-none focus:ring-2 transition-all ${
                        formErrors.location
                          ? 'border-rose-400 focus:ring-rose-500 bg-rose-50/30'
                          : 'border-slate-300 focus:ring-indigo-500 focus:border-indigo-500'
                      }`}
                    />
                  </div>
                  {formErrors.location && (
                    <p className="text-xs text-rose-500 mt-1">{formErrors.location}</p>
                  )}
                </div>

                {/* Salary Field */}
                <div>
                  <label className="block text-sm font-semibold text-slate-700 mb-1">
                    Salary (USD) <span className="text-rose-500">*</span>
                  </label>
                  <div className="relative">
                    <DollarSign className="w-5 h-5 text-slate-400 absolute left-3 top-1/2 -translate-y-1/2" />
                    <input
                      type="number"
                      placeholder="e.g. 75000"
                      value={formData.salary}
                      onChange={(e) => setFormData({ ...formData, salary: e.target.value })}
                      className={`w-full pl-10 pr-4 py-2.5 text-sm border rounded-lg focus:outline-none focus:ring-2 transition-all ${
                        formErrors.salary
                          ? 'border-rose-400 focus:ring-rose-500 bg-rose-50/30'
                          : 'border-slate-300 focus:ring-indigo-500 focus:border-indigo-500'
                      }`}
                    />
                  </div>
                  {formErrors.salary && (
                    <p className="text-xs text-rose-500 mt-1">{formErrors.salary}</p>
                  )}
                </div>

                {/* Action Buttons */}
                <div className="pt-4 flex items-center justify-end gap-3 border-t border-slate-100">
                  <button
                    type="button"
                    onClick={handleReset}
                    className="px-4 py-2.5 text-sm font-medium text-slate-600 hover:text-slate-900 bg-slate-100 hover:bg-slate-200 rounded-lg transition-colors"
                  >
                    Reset Form
                  </button>

                  <button
                    type="submit"
                    className="px-6 py-2.5 text-sm font-medium text-white bg-indigo-600 hover:bg-indigo-700 rounded-lg shadow-sm focus:ring-2 focus:ring-indigo-500 focus:ring-offset-2 transition-colors"
                  >
                    Submit Record
                  </button>
                </div>

              </form>
            </div>
          </div>
        )}

      </main>

      {/* Global Footer */}
      <footer className="bg-white border-t border-slate-200 py-4 mt-auto">
        <div className="max-w-7xl mx-auto px-4 text-center text-xs text-slate-500">
          &copy; {new Date().getFullYear()} WorkPulse Employee Portal. All rights reserved.
        </div>
      </footer>

    </div>
  );
}
