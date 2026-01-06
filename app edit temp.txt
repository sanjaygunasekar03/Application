import React, { useState } from 'react';
import {
  Phone, X, Menu, Search, Filter, Clock, CheckCircle, AlertCircle, User, Settings,
  HelpCircle, LogOut, Plus, ChevronDown, RefreshCw, Shield, Home, FileText, File, Users,
  ChevronRight, Eye, EyeOff, Loader, Calendar, XCircle, Check, ArrowLeft, ArrowRight,
  Save, Edit, Trash2, MoreVertical, Mail, PhoneCall, MessageSquare, Star, StarHalf,
  ChevronUp, ChevronLeft
} from 'lucide-react';

const App = () => {
  const [currentView, setCurrentView] = useState('signIn');
  const [isMenuOpen, setIsMenuOpen] = useState(true);
  const [isModalOpen, setIsModalOpen] = useState(false);
  const [showPassword, setShowPassword] = useState(false);
  const [activeDashboardTab, setActiveDashboardTab] = useState('dashboard');
  const [loading, setLoading] = useState(false);
  const [selectedTimeframe, setSelectedTimeframe] = useState('last30Days');
  const [searchTerm, setSearchTerm] = useState('');
  const [filterOpen, setFilterOpen] = useState(false);
  const [callType, setCallType] = useState('claimStatus');
  const [timeframeDropdownOpen, setTimeframeDropdownOpen] = useState(false);
  const [inQueueTimeframe, setInQueueTimeframe] = useState('today');
  const [inQueueDropdownOpen, setInQueueDropdownOpen] = useState(false);
  const [sortBy, setSortBy] = useState("most");
  const [ivrOnly, setIvrOnly] = useState(false);
  const [sortDropdownOpen, setSortDropdownOpen] = useState(false);
  const [createTemplateDropdownOpen, setCreateTemplateDropdownOpen] = useState(false);

  const [callBatchData, setCallBatchData] = useState({
    title: '',
    description: '',
    callType: 'claimStatus',
    recipient: 'insurance',
    target: 'patient',
    date: new Date().toISOString().split('T')[0],
    time: '09:00',
    duration: '30',
    contacts: []
  });

  const [newBatchId, setNewBatchId] = useState(1000);
  const [batches, setBatches] = useState([
    { id: 'B-1011', title: 'IVR Batch 1205 Part3', date: 'Dec 20th, 2025', calls: '83 / 98', info: '0', status: 'Review', type: 'claimStatus', created: '12/5/2025' },
    { id: 'B-1010', title: 'IVR Batch 1205 Part1', date: 'Dec 18th, 2025', calls: '9 / 19', info: '0', status: 'Review', type: 'claimStatus', created: '12/5/2025' },
    { id: 'B-1009', title: 'IVR Batch 1205 Part2', date: 'Dec 5th, 2025', calls: '3 / 20', info: '0', status: 'Review', type: 'claimStatus', created: '12/5/2025' },
    { id: 'B-1005', title: 'BHS IVR Sample Claim Wise 1120', date: 'Nov 21st, 2025', calls: '15 / 17', info: '0', status: 'Review', type: 'claimStatus', created: '11/21/2025' },
  ]);

  const templates = [
    { id: 1, title: "New Template - AC Jan 05, 2026", datapoints: "1–4 Datapoints", goal: "CS Check", type: "IVR Only", created: "1/5/2026" },
    { id: 2, title: "New Template - AC Jan 05, 2026", datapoints: "1–4 Datapoints", goal: "CS Check", type: "IVR Only", created: "1/5/2026" },
    { id: 3, title: "New Template - AC Jan 02, 2026", datapoints: "1–2 Questions", goal: "CS Check", type: "IVR Only", created: "1/2/2026" }
  ];

  const timeframes = [
    { id: 'last7Days', name: 'Last 7 Days' },
    { id: 'last30Days', name: 'Last 30 Days' },
    { id: 'last6Months', name: 'Last 6 Months' },
    { id: 'allTime', name: 'All Time' }
  ];

  const inQueueTimeframes = [
    { id: 'today', name: 'Today' },
    { id: 'thisWeek', name: 'This Week' },
    { id: 'thisMonth', name: 'This Month' }
  ];

  const callTypes = [
    { id: 'claimStatus', name: 'Claim Status', description: 'Call insurance to verify the status of a claim', recipient: 'Insurance', target: 'Patient' },
    { id: 'paymentStatus', name: 'Payment Status', description: 'Verify payment status for a claim', recipient: 'Insurance', target: 'Patient' },
    { id: 'appointment', name: 'Appointment', description: 'Schedule or confirm patient appointments', recipient: 'Patient', target: 'Patient' },
    { id: 'followUp', name: 'Follow Up', description: 'Follow up on previous interactions', recipient: 'Insurance', target: 'Patient' },
    { id: 'eligibility', name: 'Eligibility Check', description: 'Verify patient insurance eligibility', recipient: 'Insurance', target: 'Patient' },
    { id: 'benefits', name: 'Benefits Inquiry', description: 'Check specific patient benefits', recipient: 'Insurance', target: 'Patient' }
  ];

  const topContacts = [
    { id: 1, name: 'MEDICARE FL I', phone: '+18778474992', status: 'Claim Status (IVR)', calls: 98, percentage: 85, lastContact: '12/20/2025', rating: 4.5 },
    { id: 2, name: 'HUMANA I', phone: '+18003677587', status: 'Claim Status (IVR)', calls: 7, percentage: 0, lastContact: '12/18/2025', rating: 3.8 },
    { id: 3, name: 'TRICARE FOR LIFE I', phone: '+18667730404', status: 'Claim Status (IVR)', calls: 6, percentage: 83, lastContact: '12/18/2025', rating: 4.2 },
    { id: 4, name: 'AETNA I', phone: '+18886323862', status: 'Claim Status (IVR)', calls: 4, percentage: 50, lastContact: '12/17/2025', rating: 4.0 },
  ];

  const batchStatuses = ['Draft', 'In Queue', 'Review', 'Completed'];
  const callStatuses = ['Completed', 'In Progress', 'Review', 'Failed', 'Scheduled'];
  const callGoals = ['CS Check', 'Payment Verification', 'Eligibility Check', 'Benefits Inquiry'];

  const handleSignIn = () => {
    setLoading(true);
    setTimeout(() => {
      setLoading(false);
      setCurrentView('dashboard');
    }, 1000);
  };

  const handleCreateCallBatch = () => setIsModalOpen(true);

  const handleCallTypeSelect = (type) => {
    setCallType(type);
    setCallBatchData(prev => ({
      ...prev,
      callType: type,
      recipient: callTypes.find(t => t.id === type)?.recipient || '',
      target: callTypes.find(t => t.id === type)?.target || ''
    }));
  };

  const handleSaveCallBatch = () => {
    const newBatch = {
      id: `B-${newBatchId}`,
      title: callBatchData.title || `New Batch ${newBatchId}`,
      date: new Date().toLocaleDateString('en-US', { month: 'short', day: '2-digit', year: 'numeric' }),
      calls: '0 / 0',
      info: '0',
      status: 'Draft',
      type: callBatchData.callType,
      created: new Date().toLocaleDateString('MM/dd/yyyy')
    };
    setBatches(prev => [newBatch, ...prev]);
    setNewBatchId(prev => prev + 1);
    setIsModalOpen(false);
  };

  // --- RENDER FUNCTIONS ---
  const renderTemplates = () => (
    <div className="p-6 space-y-6">
      <h1 className="text-2xl font-bold text-gray-800">Edit Call Templates</h1>
      <div className="flex justify-between items-center">
        <div className="flex items-center space-x-3">
          <div className="relative">
            <button
              onClick={() => setSortDropdownOpen(!sortDropdownOpen)}
              className="px-4 py-2 border border-gray-300 rounded-lg flex items-center"
            >
              {sortBy === "most" ? "Most Relevant" : sortBy === "alpha" ? "Alphabetical" : "Newest"}
              <ChevronDown className="ml-2 w-4 h-4" />
            </button>
            {sortDropdownOpen && (
              <div className="absolute mt-2 w-48 bg-white border border-gray-200 rounded shadow-lg z-10">
                <button
                  onClick={() => { setSortBy("most"); setSortDropdownOpen(false); }}
                  className="block w-full text-left px-4 py-2 hover:bg-gray-50"
                >Most Relevant</button>
                <button
                  onClick={() => { setSortBy("alpha"); setSortDropdownOpen(false); }}
                  className="block w-full text-left px-4 py-2 hover:bg-gray-50"
                >Alphabetical</button>
                <button
                  onClick={() => { setSortBy("new"); setSortDropdownOpen(false); }}
                  className="block w-full text-left px-4 py-2 hover:bg-gray-50"
                >Newest</button>
              </div>
            )}
          </div>
          <label className="flex items-center space-x-2">
            <input
              type="checkbox"
              checked={ivrOnly}
              onChange={() => setIvrOnly(!ivrOnly)}
              className="form-checkbox h-4 w-4 text-blue-600 rounded"
            />
            <span className="text-sm text-gray-700">IVR Only</span>
          </label>
        </div>
        <div className="relative">
          <button
            onClick={() => setCreateTemplateDropdownOpen(!createTemplateDropdownOpen)}
            className="bg-blue-600 text-white px-4 py-2 rounded-lg flex items-center"
          >
            Create new template
            <ChevronDown className="ml-2 w-4 h-4" />
          </button>
          {createTemplateDropdownOpen && (
            <div className="absolute right-0 mt-2 w-56 bg-white border border-gray-200 rounded shadow-lg z-10">
              <button className="block w-full text-left px-4 py-2 hover:bg-gray-50">Claim Status — CS Check</button>
              <button className="block w-full text-left px-4 py-2 hover:bg-gray-50">Claim Status (IVR) — CS Check</button>
            </div>
          )}
        </div>
      </div>
      <div className="space-y-4">
        {templates
          .filter(t => !ivrOnly || t.type === "IVR Only")
          .map(t => (
            <div key={t.id} className="border border-gray-200 rounded-xl p-4 bg-white shadow-sm">
              <div className="flex justify-between">
                <div>
                  <h3 className="font-semibold text-gray-900">{t.title}</h3>
                  <p className="text-sm text-gray-500">{t.datapoints}</p>
                  <p className="text-sm text-gray-700">{t.goal}</p>
                  <p className="text-sm text-gray-600">{t.type}</p>
                </div>
                <button className="bg-blue-600 text-white px-4 py-2 rounded-lg hover:bg-blue-700 text-sm">Edit</button>
              </div>
              <p className="text-xs mt-2 text-gray-500">Created {t.created}</p>
            </div>
          ))}
      </div>
    </div>
  );

  const renderSignInScreen = () => (
    <div className="min-h-screen flex items-center justify-center bg-gray-50 px-4">
      <div className="w-full max-w-md bg-white rounded-2xl shadow-xl overflow-hidden">
        <div className="p-8">
          <div className="flex items-center justify-center mb-8">
            <div className="bg-blue-100 p-3 rounded-xl mr-3">
              <Phone className="w-8 h-8 text-blue-600" />
            </div>
            <h1 className="text-2xl font-bold text-gray-900">Standard Practice</h1>
          </div>
          <h2 className="text-2xl font-bold text-center text-gray-900 mb-2">Sign In</h2>
          <p className="text-center text-gray-500 mb-8">
            By continuing, you are indicating that you agree to our{' '}
            <a href="#" className="text-blue-600 hover:underline">Terms of Use & Privacy Policy</a>.
          </p>
          <div className="space-y-4 mb-6">
            <button className="w-full flex items-center justify-center px-4 py-3 border border-gray-300 rounded-xl hover:bg-gray-50 transition-colors">
              <div className="w-5 h-5 bg-red-500 rounded-full mr-3 flex items-center justify-center">
                <span className="text-white text-xs font-bold">G</span>
              </div>
              <span className="font-medium">Continue with Google</span>
            </button>
            <button className="w-full flex items-center justify-center px-4 py-3 border border-gray-300 rounded-xl hover:bg-gray-50 transition-colors">
              <div className="w-5 h-5 bg-blue-500 rounded-full mr-3 flex items-center justify-center">
                <span className="text-white text-xs font-bold">M</span>
              </div>
              <span className="font-medium">Continue with Microsoft Account</span>
            </button>
          </div>
          <div className="flex items-center my-6">
            <div className="flex-grow border-t border-gray-300"></div>
            <span className="mx-4 text-gray-500 text-sm">OR</span>
            <div className="flex-grow border-t border-gray-300"></div>
          </div>
          <div className="space-y-4 mb-6">
            <div>
              <label className="block text-sm font-medium text-gray-700 mb-1">Business email</label>
              <input
                type="email"
                defaultValue="alvin@bristolhcs.com"
                className="w-full px-4 py-3 border border-gray-300 rounded-xl focus:ring-2 focus:ring-blue-500 focus:border-blue-500"
              />
            </div>
            <div>
              <label className="block text-sm font-medium text-gray-700 mb-1">Password</label>
              <div className="relative">
                <input
                  type={showPassword ? "text" : "password"}
                  className="w-full px-4 py-3 border border-gray-300 rounded-xl focus:ring-2 focus:ring-blue-500 focus:border-blue-500 pr-12"
                />
                <button
                  type="button"
                  onClick={() => setShowPassword(!showPassword)}
                  className="absolute right-3 top-1/2 transform -translate-y-1/2 text-gray-400 hover:text-gray-600"
                >
                  {showPassword ? <EyeOff className="w-5 h-5" /> : <Eye className="w-5 h-5" />}
                </button>
              </div>
              <div className="text-right mt-1">
                <a href="#" className="text-sm text-blue-600 hover:underline">Forgot password?</a>
              </div>
            </div>
          </div>
          <button
            onClick={handleSignIn}
            disabled={loading}
            className="w-full bg-gradient-to-r from-blue-600 to-blue-700 text-white font-medium py-3 rounded-xl hover:from-blue-700 hover:to-blue-800 transition-colors flex items-center justify-center"
          >
            {loading ? (
              <>
                <Loader className="w-5 h-5 mr-2 animate-spin" />
                Signing in...
              </>
            ) : (
              <>
                Continue
                <ChevronRight className="w-4 h-4 ml-2" />
              </>
            )}
          </button>
          <p className="text-center text-gray-500 mt-6">
            Don't have an account?{' '}
            <a href="#" className="text-blue-600 font-medium hover:underline">Sign up</a>
          </p>
        </div>
      </div>
    </div>
  );

  const renderDashboard = () => (
    <div className="p-6 space-y-6">
      <div className="flex justify-between items-center">
        <h1 className="text-2xl font-bold text-gray-800">Call Dashboard</h1>
        <div className="flex items-center space-x-4">
          <span className="text-sm text-gray-500">Updated 1/6/2026 2:03 AM</span>
          <button className="flex items-center text-blue-600 hover:text-blue-800">
            <RefreshCw className="w-4 h-4 mr-1" /> Refresh
          </button>
          <div className="relative">
            <select className="pl-3 pr-8 py-2 border border-gray-300 rounded-lg focus:outline-none focus:ring-2 focus:ring-blue-500">
              <option>Recently Viewed</option>
            </select>
            <ChevronDown className="w-4 h-4 absolute right-3 top-1/2 transform -translate-y-1/2 text-gray-500" />
          </div>
          <button
            onClick={handleCreateCallBatch}
            className="bg-blue-600 text-white px-4 py-2 rounded-lg hover:bg-blue-700 flex items-center"
          >
            <Plus className="w-4 h-4 mr-1" /> Create Call Batch
          </button>
        </div>
      </div>
      <div className="flex space-x-4 mb-6">
        <div className="relative">
          <button
            onClick={() => setTimeframeDropdownOpen(!timeframeDropdownOpen)}
            className="px-4 py-2 bg-gray-50 rounded-lg flex items-center"
          >
            <span className="mr-2">Last 30 Days</span>
            <ChevronDown className="w-4 h-4 text-gray-500" />
          </button>
          {timeframeDropdownOpen && (
            <div className="absolute mt-2 w-48 bg-white border border-gray-200 rounded-lg shadow-lg z-10">
              {timeframes.map(timeframe => (
                <button
                  key={timeframe.id}
                  className="w-full text-left px-4 py-2 hover:bg-gray-50"
                  onClick={() => {
                    setSelectedTimeframe(timeframe.id);
                    setTimeframeDropdownOpen(false);
                  }}
                >
                  {timeframe.name}
                </button>
              ))}
            </div>
          )}
        </div>
        {topContacts.map(contact => (
          <div key={contact.id} className="flex-1 bg-white border border-gray-200 rounded-lg p-3">
            <div className="flex justify-between items-start">
              <div>
                <h3 className="font-medium text-gray-900">{contact.name} | {contact.phone}</h3>
                <div className="flex items-center text-xs text-gray-500 mt-1">
                  <span className="bg-purple-100 text-purple-800 px-1.5 py-0.5 rounded mr-1">CS Check</span>
                  <span className="flex items-center">
                    <AlertCircle className="w-3 h-3 mr-1 text-yellow-500" />
                    {contact.percentage}% avg
                  </span>
                </div>
              </div>
              <div className="text-xs bg-blue-100 text-blue-800 px-2 py-1 rounded">{contact.calls} calls</div>
            </div>
          </div>
        ))}
      </div>
      <div className="grid grid-cols-1 md:grid-cols-4 gap-6">
        {batchStatuses.map((section, index) => (
          <div key={index} className="bg-white border border-gray-200 rounded-xl overflow-hidden">
            <div className="bg-gray-900 text-white p-3 flex justify-between items-center">
              <div className="flex items-center">
                {index === 0 && <FileText className="w-4 h-4 mr-2" />}
                {index === 1 && <Clock className="w-4 h-4 mr-2" />}
                {index === 2 && <CheckCircle className="w-4 h-4 mr-2" />}
                {index === 3 && <CheckCircle className="w-4 h-4 mr-2" />}
                <h2 className="font-semibold">{section}</h2>
              </div>
              <button className="text-sm text-blue-300 hover:text-blue-200">
                View all ({section === 'Review' ? batches.filter(b => b.status === 'Review').length : 0})
              </button>
            </div>
            <div className="p-4 min-h-[300px]">
              {section === 'Review' ? (
                <div className="space-y-3">
                  {batches.filter(b => b.status === 'Review').map(batch => (
                    <div key={batch.id} className="border border-gray-200 rounded-lg p-3 hover:shadow-md transition-shadow">
                      <div className="flex justify-between items-start mb-2">
                        <h3 className="font-medium text-gray-900">{batch.title}</h3>
                        <span className="text-xs bg-purple-100 text-purple-800 px-2 py-1 rounded">Claim Status (IVR)</span>
                      </div>
                      <div className="flex justify-between text-xs text-gray-500 mb-2">
                        <div className="flex items-center">
                          <div className="flex items-center mr-3">
                            <div className="w-5 h-5 rounded-full bg-blue-100 flex items-center justify-center mr-1">
                              <Phone className="w-3 h-3 text-blue-600" />
                            </div>
                            {batch.calls}
                          </div>
                          <div className="flex items-center">
                            <div className="w-5 h-5 rounded-full bg-gray-100 flex items-center justify-center mr-1">
                              <MessageSquare className="w-3 h-3 text-gray-500" />
                            </div>
                            {batch.info} Info
                          </div>
                        </div>
                        <div className="flex items-center">
                          <Calendar className="w-3 h-3 mr-1 text-gray-500" />
                          {batch.date}
                        </div>
                      </div>
                      <div className="flex justify-between">
                        <div className="flex items-center text-xs text-gray-500">
                          <div className="w-5 h-5 rounded-full bg-gray-100 flex items-center justify-center mr-1">
                            <User className="w-3 h-3 text-gray-500" />
                          </div>
                          {batch.created}
                        </div>
                        <button className="text-xs bg-blue-100 text-blue-800 px-2 py-1 rounded hover:bg-blue-200">Review</button>
                      </div>
                    </div>
                  ))}
                </div>
              ) : section === 'In Queue' ? (
                <div className="space-y-3">
                  <div className="flex justify-between items-center mb-2">
                    <div className="flex items-center">
                      <Clock className="w-4 h-4 mr-2 text-blue-500" />
                      <div className="relative">
                        <button
                          onClick={() => setInQueueDropdownOpen(!inQueueDropdownOpen)}
                          className="text-sm text-blue-600 flex items-center"
                        >
                          {inQueueTimeframes.find(t => t.id === inQueueTimeframe)?.name || 'Today'}
                          <ChevronDown className="w-3 h-3 ml-1" />
                        </button>
                        {inQueueDropdownOpen && (
                          <div className="absolute mt-2 w-48 bg-white border border-gray-200 rounded-lg shadow-lg z-10">
                            {inQueueTimeframes.map(timeframe => (
                              <button
                                key={timeframe.id}
                                className="w-full text-left px-4 py-2 hover:bg-gray-50"
                                onClick={() => {
                                  setInQueueTimeframe(timeframe.id);
                                  setInQueueDropdownOpen(false);
                                }}
                              >
                                {timeframe.name}
                              </button>
                            ))}
                          </div>
                        )}
                      </div>
                    </div>
                    <div className="flex space-x-2">
                      <button className="w-5 h-5 rounded-full border border-gray-300 flex items-center justify-center">
                        <ChevronLeft className="w-3 h-3" />
                      </button>
                      <button className="w-5 h-5 rounded-full border border-gray-300 flex items-center justify-center">
                        <ChevronRight className="w-3 h-3" />
                      </button>
                    </div>
                  </div>
                  {batches.filter(b => b.status === 'In Queue').length > 0 ? (
                    <div className="space-y-3">
                      {batches.filter(b => b.status === 'In Queue').map(batch => (
                        <div key={batch.id} className="border border-gray-200 rounded-lg p-3 hover:shadow-md transition-shadow">
                          <div className="flex justify-between items-start mb-2">
                            <h3 className="font-medium text-gray-900">{batch.title}</h3>
                            <span className="text-xs bg-purple-100 text-purple-800 px-2 py-1 rounded">Claim Status (IVR)</span>
                          </div>
                          <div className="flex justify-between text-xs text-gray-500 mb-2">
                            <div className="flex items-center">
                              <div className="flex items-center mr-3">
                                <div className="w-5 h-5 rounded-full bg-blue-100 flex items-center justify-center mr-1">
                                  <Phone className="w-3 h-3 text-blue-600" />
                                </div>
                                {batch.calls}
                              </div>
                              <div className="flex items-center">
                                <div className="w-5 h-5 rounded-full bg-gray-100 flex items-center justify-center mr-1">
                                  <MessageSquare className="w-3 h-3 text-gray-500" />
                                </div>
                                {batch.info} Info
                              </div>
                            </div>
                            <div className="flex items-center">
                              <Calendar className="w-3 h-3 mr-1 text-gray-500" />
                              {batch.date}
                            </div>
                          </div>
                          <div className="flex justify-between">
                            <div className="flex items-center text-xs text-gray-500">
                              <div className="w-5 h-5 rounded-full bg-gray-100 flex items-center justify-center mr-1">
                                <User className="w-3 h-3 text-gray-500" />
                              </div>
                              {batch.created}
                            </div>
                            <button className="text-xs bg-blue-100 text-blue-800 px-2 py-1 rounded hover:bg-blue-200">Review</button>
                          </div>
                        </div>
                      ))}
                    </div>
                  ) : (
                    <div className="h-full flex flex-col items-center justify-center text-center p-4">
                      <Phone className="w-12 h-12 text-gray-300 mb-3" />
                      <p className="text-gray-500 mb-3">No calls in queue</p>
                      <button
                        onClick={() => setBatches(prev => [{ id: `B-${newBatchId}`, title: `Queue Batch ${newBatchId}`, date: new Date().toLocaleDateString('en-US', { month: 'short', day: '2-digit', year: 'numeric' }), calls: '0 / 0', info: '0', status: 'In Queue', type: 'claimStatus', created: new Date().toLocaleDateString('MM/dd/yyyy') }, ...prev])}
                        className="text-blue-600 hover:text-blue-800 flex items-center"
                      >
                        <Plus className="w-4 h-4 mr-1" /> Create call batch
                      </button>
                    </div>
                  )}
                </div>
              ) : (
                <div className="h-full flex flex-col items-center justify-center text-center p-4">
                  <Phone className="w-12 h-12 text-gray-300 mb-3" />
                  <p className="text-gray-500 mb-3">No {section.toLowerCase()} batches</p>
                  <button
                    onClick={handleCreateCallBatch}
                    className="text-blue-600 hover:text-blue-800 flex items-center"
                  >
                    <Plus className="w-4 h-4 mr-1" /> Create call batch
                  </button>
                </div>
              )}
            </div>
          </div>
        ))}
      </div>
    </div>
  );

  const renderCallTypeModal = () => (
    <div className="fixed inset-0 bg-black bg-opacity-50 flex items-center justify-center p-4 z-50">
      <div className="bg-white rounded-xl w-full max-w-2xl">
        <div className="p-6">
          <div className="flex justify-between items-start mb-6">
            <div>
              <h2 className="text-xl font-bold text-gray-900 mb-1">Select Call Type</h2>
              <p className="text-gray-600">What is the goal of this call batch?</p>
              <p className="text-gray-500 mt-1">Pick what type of call you would like Standard Practice to make on your behalf.</p>
            </div>
            <button onClick={() => setIsModalOpen(false)} className="text-gray-400 hover:text-gray-600">
              <X className="w-6 h-6" />
            </button>
          </div>
          <div className="grid grid-cols-1 md:grid-cols-2 gap-6 mb-8">
            <div className="border border-gray-200 rounded-xl p-5 hover:border-blue-500 transition-colors">
              <div className="flex justify-between items-start mb-3">
                <div>
                  <h3 className="font-semibold text-gray-900">Claim Status</h3>
                  <p className="text-sm text-gray-500 mt-1">Call insurance to verify the status of a claim</p>
                </div>
                <Shield className="w-5 h-5 text-blue-500" />
              </div>
              <div className="text-xs text-gray-500 mb-3">
                <div className="flex items-center mb-1">
                  <span className="w-1 h-1 bg-gray-400 rounded-full mr-2"></span>
                  <span>To: Insurance</span>
                </div>
                <div className="flex items-center">
                  <span className="w-1 h-1 bg-gray-400 rounded-full mr-2"></span>
                  <span>Re: Patient</span>
                </div>
              </div>
              <button
                onClick={() => handleCallTypeSelect('claimStatus')}
                className={`w-full py-2 rounded-lg font-medium transition-colors ${
                  callType === 'claimStatus'
                    ? 'bg-blue-600 text-white'
                    : 'border-2 border-blue-600 text-blue-600 hover:bg-blue-50'
                }`}
              >
                {callType === 'claimStatus' ? 'Selected' : 'Select Call Type'}
              </button>
            </div>
            <div className="border border-gray-200 rounded-xl p-5 hover:border-blue-500 transition-colors">
              <div className="flex justify-between items-start mb-3">
                <div>
                  <h3 className="font-semibold text-gray-900">Not seeing your call type?</h3>
                </div>
              </div>
              <button className="w-full border border-blue-600 text-blue-600 py-2 rounded-lg hover:bg-blue-50">
                Request New Call Type
              </button>
            </div>
          </div>
          <button
            onClick={handleSaveCallBatch}
            className="w-full py-3 bg-blue-600 text-white rounded-lg hover:bg-blue-700 font-medium flex items-center justify-center"
          >
            Continue <ChevronRight className="w-4 h-4 ml-2" />
          </button>
        </div>
      </div>
    </div>
  );

  if (currentView === 'signIn') {
    return renderSignInScreen();
  }

  return (
    <div className="flex h-screen bg-gray-50">
      {/* Sidebar */}
      <aside className={`${isMenuOpen ? 'w-64' : 'w-16'} bg-white border-r border-gray-200 transition-all duration-300 flex flex-col`}>
        <div className="p-4 flex items-center justify-between border-b border-gray-200">
          {isMenuOpen && (
            <div className="flex items-center">
              <div className="bg-blue-100 p-2 rounded-lg mr-3">
                <Phone className="w-5 h-5 text-blue-600" />
              </div>
              <h1 className="font-bold text-gray-900">Standard Practice</h1>
            </div>
          )}
          <button
            onClick={() => setIsMenuOpen(!isMenuOpen)}
            className="text-gray-500 hover:text-gray-700"
          >
            {isMenuOpen ? <X className="w-5 h-5" /> : <Menu className="w-5 h-5" />}
          </button>
        </div>
        <div className="px-4 py-4 border-b border-gray-200">
          {isMenuOpen && (
            <div className="bg-blue-50 border border-blue-200 rounded-xl p-3">
              <div className="flex items-center">
                <div className="h-10 w-10 rounded-full bg-blue-100 flex items-center justify-center mr-3">
                  <User className="w-5 h-5 text-blue-600" />
                </div>
                <div>
                  <h3 className="font-medium text-gray-900">Bristol Healthcare Services</h3>
                  <p className="text-sm text-gray-500">Alvin Cortez</p>
                </div>
              </div>
            </div>
          )}
        </div>
        <nav className="flex-1 px-2 py-4 space-y-1 overflow-y-auto">
          {[
            { name: 'Dashboard', icon: Home, active: activeDashboardTab === 'dashboard' },
            { name: 'Calls', icon: Phone, active: activeDashboardTab === 'calls' },
            { name: 'Batches', icon: FileText, active: activeDashboardTab === 'batches' },
            { name: 'Templates', icon: File, active: activeDashboardTab === 'templates' },
            { name: 'Contact Insights', icon: Users, active: activeDashboardTab === 'insights' },
          ].map((item) => (
            <button
              key={item.name}
              onClick={() => setActiveDashboardTab(item.name.toLowerCase().replace(' ', ''))}
              className={`w-full flex items-center px-3 py-3 rounded-lg transition-colors ${
                item.active
                  ? 'bg-blue-50 text-blue-600'
                  : 'text-gray-600 hover:bg-gray-50 hover:text-gray-900'
              }`}
            >
              <item.icon className="w-5 h-5 mr-3" />
              {isMenuOpen && <span className="font-medium">{item.name}</span>}
            </button>
          ))}
          <button
            onClick={handleCreateCallBatch}
            className="w-full flex items-center px-3 py-3 rounded-lg text-gray-600 hover:bg-gray-50 hover:text-gray-900 mt-4"
          >
            <Plus className="w-5 h-5 mr-3" />
            {isMenuOpen && <span className="font-medium">Create Call Batch</span>}
          </button>
        </nav>
        <div className="border-t border-gray-200 p-4 space-y-1">
          <button className="w-full flex items-center px-3 py-3 rounded-lg text-gray-600 hover:bg-gray-50 hover:text-gray-900">
            <Settings className="w-5 h-5 mr-3" />
            {isMenuOpen && <span className="font-medium">Settings</span>}
          </button>
          <button className="w-full flex items-center px-3 py-3 rounded-lg text-gray-600 hover:bg-gray-50 hover:text-gray-900">
            <HelpCircle className="w-5 h-5 mr-3" />
            {isMenuOpen && <span className="font-medium">Help Center</span>}
          </button>
          <button
            onClick={() => setCurrentView('signIn')}
            className="w-full flex items-center px-3 py-3 rounded-lg text-gray-600 hover:bg-gray-50 hover:text-gray-900 mt-2"
          >
            <LogOut className="w-5 h-5 mr-3" />
            {isMenuOpen && <span className="font-medium">Log Out</span>}
          </button>
        </div>
      </aside>

      {/* Main Content */}
      <main className="flex flex-col flex-1 overflow-hidden">
        <header className="bg-white border-b border-gray-200 h-16 flex items-center justify-between px-6">
          <div className="flex items-center">
            <button className="text-gray-500 hover:text-gray-700 mr-4 md:hidden">
              <Menu className="w-5 h-5" />
            </button>
            <div className="text-xs text-gray-500">
              Outside Call Hours | Regular call hours: Mon-Fri, 11am-9pm EST | IVR call hours: 24/7 | Holidays may affect call hours.
            </div>
          </div>
          <div className="flex items-center space-x-4">
            <span className="text-sm text-gray-500 hidden md:block">Contact us</span>
            <div className="h-8 w-8 rounded-full bg-blue-100 flex items-center justify-center">
              <User className="w-4 h-4 text-blue-600" />
            </div>
          </div>
        </header>
        <div className="flex-1 overflow-y-auto">
          {activeDashboardTab === 'dashboard' && renderDashboard()}
          {activeDashboardTab === 'batches' && (
            <div className="p-6 space-y-6">
              <div className="flex justify-between items-center">
                <h1 className="text-2xl font-bold text-gray-800">Search Batches</h1>
                <div className="flex space-x-4">
                  <button
                    onClick={handleCreateCallBatch}
                    className="bg-blue-600 text-white px-4 py-2 rounded-lg hover:bg-blue-700 flex items-center"
                  >
                    <Plus className="w-4 h-4 mr-1" /> Create Call Batch
                  </button>
                </div>
              </div>
              <div className="flex space-x-4 mb-6">
                <div className="flex-1 max-w-xs">
                  <select className="w-full px-3 py-2 border border-gray-300 rounded-lg focus:outline-none focus:ring-2 focus:ring-blue-500">
                    <option>Batch ID</option>
                    <option>Creation Date</option>
                    <option>Status</option>
                    <option>Call Type</option>
                  </select>
                </div>
                <div className="flex-1">
                  <div className="relative">
                    <input
                      type="text"
                      placeholder="Search batches..."
                      className="w-full px-3 py-2 border border-gray-300 rounded-lg focus:outline-none focus:ring-2 focus:ring-blue-500 pl-10"
                      value={searchTerm}
                      onChange={(e) => setSearchTerm(e.target.value)}
                    />
                    <Search className="w-4 h-4 absolute left-3 top-1/2 transform -translate-y-1/2 text-gray-400" />
                  </div>
                </div>
                <button
                  onClick={() => setFilterOpen(!filterOpen)}
                  className="flex items-center px-4 py-2 border border-gray-300 rounded-lg hover:bg-gray-50"
                >
                  <Filter className="w-4 h-4 mr-2" /> Filter
                </button>
              </div>
              {filterOpen && (
                <div className="bg-white border border-gray-200 rounded-xl p-6 mb-6">
                  <div className="grid grid-cols-1 md:grid-cols-4 gap-6">
                    <div>
                      <label className="block text-sm font-medium text-gray-700 mb-1">Call Type</label>
                      <select className="w-full px-3 py-2 border border-gray-300 rounded-lg focus:outline-none focus:ring-2 focus:ring-blue-500">
                        <option>All Types</option>
                        <option>Claim Status</option>
                        <option>Payment Status</option>
                        <option>Appointment</option>
                      </select>
                    </div>
                    <div>
                      <label className="block text-sm font-medium text-gray-700 mb-1">Status</label>
                      <select className="w-full px-3 py-2 border border-gray-300 rounded-lg focus:outline-none focus:ring-2 focus:ring-blue-500">
                        <option>All Statuses</option>
                        <option>Draft</option>
                        <option>In Queue</option>
                        <option>Review</option>
                        <option>Completed</option>
                      </select>
                    </div>
                    <div>
                      <label className="block text-sm font-medium text-gray-700 mb-1">Date Range</label>
                      <select className="w-full px-3 py-2 border border-gray-300 rounded-lg focus:outline-none focus:ring-2 focus:ring-blue-500">
                        <option>Last 7 Days</option>
                        <option>Last 30 Days</option>
                        <option>Last 6 Months</option>
                        <option>All Time</option>
                      </select>
                    </div>
                    <div>
                      <label className="block text-sm font-medium text-gray-700 mb-1">Call Goal</label>
                      <select className="w-full px-3 py-2 border border-gray-300 rounded-lg focus:outline-none focus:ring-2 focus:ring-blue-500">
                        <option>All Goals</option>
                        <option>CS Check</option>
                        <option>Payment Verification</option>
                        <option>Eligibility Check</option>
                        <option>Benefits Inquiry</option>
                      </select>
                    </div>
                  </div>
                  <div className="mt-4 flex justify-end space-x-3">
                    <button
                      onClick={() => setFilterOpen(false)}
                      className="px-4 py-2 border border-gray-300 rounded-lg hover:bg-gray-50"
                    >
                      Cancel
                    </button>
                    <button className="px-4 py-2 bg-blue-600 text-white rounded-lg hover:bg-blue-700">
                      Apply Filters
                    </button>
                  </div>
                </div>
              )}
              <div className="bg-white border border-gray-200 rounded-xl overflow-hidden">
                <div className="p-4 border-b border-gray-200">
                  <div className="flex justify-between items-center">
                    <div>{batches.length} batches total <span className="text-blue-600 ml-1">Select All</span></div>
                    <div className="flex space-x-2">
                      <button className="px-3 py-1 border border-gray-300 rounded-lg text-sm">Export</button>
                      <button className="px-3 py-1 border border-gray-300 rounded-lg text-sm">Archive</button>
                    </div>
                  </div>
                </div>
                <table className="min-w-full divide-y divide-gray-200">
                  <thead className="bg-gray-50">
                    <tr>
                      {['Created On', 'Batch ID', 'Call Type / Goal', 'Title', 'Call Date', 'Primary Info', 'Complete Calls', 'Batch Status'].map((header) => (
                        <th key={header} className="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">
                          {header}
                        </th>
                      ))}
                    </tr>
                  </thead>
                  <tbody className="bg-white divide-y divide-gray-200">
                    {batches.map((batch) => (
                      <tr key={batch.id} className="hover:bg-gray-50">
                        <td className="px-6 py-4 whitespace-nowrap text-sm text-gray-900">
                          <div className="flex items-center">
                            <div className="h-8 w-8 rounded-full bg-blue-100 flex items-center justify-center mr-3">
                              <User className="w-4 h-4 text-blue-600" />
                            </div>
                            <span>{batch.created}</span>
                          </div>
                        </td>
                        <td className="px-6 py-4 whitespace-nowrap text-sm font-medium text-gray-900">{batch.id}</td>
                        <td className="px-6 py-4 whitespace-nowrap text-sm text-gray-500">
                          <div className="flex items-center">
                            <span className={`px-2 py-0.5 rounded text-xs font-medium ${
                              batch.type === 'claimStatus' ? 'bg-purple-100 text-purple-800' :
                              batch.type === 'paymentStatus' ? 'bg-blue-100 text-blue-800' :
                              batch.type === 'appointment' ? 'bg-green-100 text-green-800' :
                              'bg-amber-100 text-amber-800'
                            }`}>
                              {callTypes.find(t => t.id === batch.type)?.name || 'Claim Status'}
                            </span>
                            <span className="ml-2">CS Check</span>
                          </div>
                        </td>
                        <td className="px-6 py-4 whitespace-nowrap text-sm text-gray-900">{batch.title}</td>
                        <td className="px-6 py-4 whitespace-nowrap text-sm text-gray-500">{batch.date}</td>
                        <td className="px-6 py-4 whitespace-nowrap text-sm text-gray-500">{batch.calls}</td>
                        <td className="px-6 py-4 whitespace-nowrap text-sm text-gray-500">{batch.calls}</td>
                        <td className="px-6 py-4 whitespace-nowrap">
                          <span className={`px-2 inline-flex text-xs leading-5 font-semibold rounded-full ${
                            batch.status === 'Review' ? 'bg-yellow-100 text-yellow-800' :
                            batch.status === 'Completed' ? 'bg-green-100 text-green-800' :
                            batch.status === 'In Queue' ? 'bg-blue-100 text-blue-800' :
                            'bg-gray-100 text-gray-800'
                          }`}>
                            {batch.status}
                          </span>
                        </td>
                      </tr>
                    ))}
                  </tbody>
                </table>
                <div className="p-4 border-t border-gray-200 flex justify-between items-center">
                  <div className="text-sm text-gray-500">
                    Showing 1 to {Math.min(10, batches.length)} of {batches.length} results
                  </div>
                  <div className="flex space-x-2">
                    <button className="px-3 py-1 border border-gray-300 rounded-lg text-sm">Previous</button>
                    <button className="px-3 py-1 border border-gray-300 rounded-lg text-sm bg-blue-600 text-white">1</button>
                    <button className="px-3 py-1 border border-gray-300 rounded-lg text-sm">2</button>
                    <button className="px-3 py-1 border border-gray-300 rounded-lg text-sm">Next</button>
                  </div>
                </div>
              </div>
            </div>
          )}
          {activeDashboardTab === 'calls' && (
            <div className="p-6 space-y-6">
              <div className="flex justify-between items-center">
                <h1 className="text-2xl font-bold text-gray-800">Search Calls</h1>
                <div className="flex space-x-4">
                  <button className="px-4 py-2 border border-gray-300 rounded-lg hover:bg-gray-50 flex items-center">
                    <Plus className="w-4 h-4 mr-1" /> Create Individual Call
                  </button>
                </div>
              </div>
              <div className="flex space-x-4 mb-6">
                <div className="flex-1 max-w-xs">
                  <select className="w-full px-3 py-2 border border-gray-300 rounded-lg focus:outline-none focus:ring-2 focus:ring-blue-500">
                    <option>Call ID</option>
                    <option>Call Date</option>
                    <option>Call Status</option>
                    <option>Call Type</option>
                  </select>
                </div>
                <div className="flex-1">
                  <div className="relative">
                    <input
                      type="text"
                      placeholder="Search calls..."
                      className="w-full px-3 py-2 border border-gray-300 rounded-lg focus:outline-none focus:ring-2 focus:ring-blue-500 pl-10"
                      value={searchTerm}
                      onChange={(e) => setSearchTerm(e.target.value)}
                    />
                    <Search className="w-4 h-4 absolute left-3 top-1/2 transform -translate-y-1/2 text-gray-400" />
                  </div>
                </div>
                <button
                  onClick={() => setFilterOpen(!filterOpen)}
                  className="flex items-center px-4 py-2 border border-gray-300 rounded-lg hover:bg-gray-50"
                >
                  <Filter className="w-4 h-4 mr-2" /> Filter
                </button>
              </div>
              {filterOpen && (
                <div className="bg-white border border-gray-200 rounded-xl p-6 mb-6">
                  <div className="grid grid-cols-1 md:grid-cols-4 gap-6">
                    <div>
                      <label className="block text-sm font-medium text-gray-700 mb-1">Call Type</label>
                      <select className="w-full px-3 py-2 border border-gray-300 rounded-lg focus:outline-none focus:ring-2 focus:ring-blue-500">
                        <option>All Types</option>
                        <option>Claim Status</option>
                        <option>Payment Status</option>
                        <option>Appointment</option>
                      </select>
                    </div>
                    <div>
                      <label className="block text-sm font-medium text-gray-700 mb-1">Status</label>
                      <select className="w-full px-3 py-2 border border-gray-300 rounded-lg focus:outline-none focus:ring-2 focus:ring-blue-500">
                        <option>All Statuses</option>
                        <option>Completed</option>
                        <option>In Progress</option>
                        <option>Review</option>
                        <option>Failed</option>
                      </select>
                    </div>
                    <div>
                      <label className="block text-sm font-medium text-gray-700 mb-1">Date Range</label>
                      <select className="w-full px-3 py-2 border border-gray-300 rounded-lg focus:outline-none focus:ring-2 focus:ring-blue-500">
                        <option>Last 7 Days</option>
                        <option>Last 30 Days</option>
                        <option>Last 6 Months</option>
                        <option>All Time</option>
                      </select>
                    </div>
                    <div>
                      <label className="block text-sm font-medium text-gray-700 mb-1">Call Goal</label>
                      <select className="w-full px-3 py-2 border border-gray-300 rounded-lg focus:outline-none focus:ring-2 focus:ring-blue-500">
                        <option>All Goals</option>
                        <option>CS Check</option>
                        <option>Payment Verification</option>
                        <option>Eligibility Check</option>
                        <option>Benefits Inquiry</option>
                      </select>
                    </div>
                  </div>
                  <div className="mt-4 flex justify-end space-x-3">
                    <button
                      onClick={() => setFilterOpen(false)}
                      className="px-4 py-2 border border-gray-300 rounded-lg hover:bg-gray-50"
                    >
                      Cancel
                    </button>
                    <button className="px-4 py-2 bg-blue-600 text-white rounded-lg hover:bg-blue-700">
                      Apply Filters
                    </button>
                  </div>
                </div>
              )}
              <div className="bg-white border border-gray-200 rounded-xl overflow-hidden">
                <div className="p-4 border-b border-gray-200">
                  <div className="flex justify-between items-center">
                    <div>175 calls total <span className="text-blue-600 ml-1">Select All</span></div>
                    <div className="flex space-x-2">
                      <button className="px-3 py-1 border border-gray-300 rounded-lg text-sm">Export</button>
                      <button className="px-3 py-1 border border-gray-300 rounded-lg text-sm">Archive</button>
                    </div>
                  </div>
                </div>
                <table className="min-w-full divide-y divide-gray-200">
                  <thead className="bg-gray-50">
                    <tr>
                      {['Call Date', 'Call ID', 'Call Type / Goal', 'Call To', 'Call Regarding', 'Practice Name', 'Primary Info', 'Info', 'Call Status'].map((header) => (
                        <th key={header} className="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">
                          {header}
                        </th>
                      ))}
                    </tr>
                  </thead>
                  <tbody className="bg-white divide-y divide-gray-200">
                    {[
                      { id: 'C-1697', date: '12/19/2025 07:53 PM', type: 'Claim Status (IVR)', to: 'Medicare fl', regarding: 'Johanna tricarico', status: 'Completed', duration: '5:22', outcome: 'Status verified' },
                      { id: 'C-1696', date: '12/19/2025 07:44 PM', type: 'Claim Status (IVR)', to: 'Medicare fl', regarding: 'Michael naranich jr', status: 'Completed', duration: '4:15', outcome: 'Status verified' },
                      { id: 'C-1695', date: '12/19/2025 07:35 PM', type: 'Claim Status (IVR)', to: 'Medicare fl', regarding: 'John f larson', status: 'In Progress', duration: '2:30', outcome: 'Left voicemail' },
                      { id: 'C-1694', date: '12/19/2025 07:23 PM', type: 'Claim Status (IVR)', to: 'Medicare fl', regarding: 'Tracy keefer', status: 'Review', duration: '3:18', outcome: 'Need follow up' },
                    ].map((call) => (
                      <tr key={call.id} className="hover:bg-gray-50">
                        <td className="px-6 py-4 whitespace-nowrap text-sm text-gray-900">{call.date}</td>
                        <td className="px-6 py-4 whitespace-nowrap text-sm font-medium text-gray-900">{call.id}</td>
                        <td className="px-6 py-4 whitespace-nowrap text-sm text-gray-500">
                          <span className="bg-purple-100 text-purple-800 px-1.5 py-0.5 rounded">
                            {call.type}
                          </span>
                        </td>
                        <td className="px-6 py-4 whitespace-nowrap text-sm text-gray-900">{call.to}</td>
                        <td className="px-6 py-4 whitespace-nowrap text-sm text-gray-500">{call.regarding}</td>
                        <td className="px-6 py-4 whitespace-nowrap text-sm text-gray-500">Allen-foot and ankle ...</td>
                        <td className="px-6 py-4 whitespace-nowrap text-sm text-gray-500">
                          {call.status === 'In Progress' ? 'Not Found' : call.status === 'Review' ? '0/1' : 'Paid'}
                        </td>
                        <td className="px-6 py-4 whitespace-nowrap text-sm text-gray-500">
                          {call.status === 'In Progress' ? '2/2' : call.status === 'Review' ? '7/8' : '7/8'}
                        </td>
                        <td className="px-6 py-4 whitespace-nowrap">
                          {call.status === 'Review' ? (
                            <span className="px-2 inline-flex text-xs leading-5 font-semibold rounded-full bg-yellow-100 text-yellow-800">
                              {call.status}
                            </span>
                          ) : call.status === 'Completed' ? (
                            <span className="px-2 inline-flex text-xs leading-5 font-semibold rounded-full bg-green-100 text-green-800">
                              {call.status}
                            </span>
                          ) : (
                            <span className="px-2 inline-flex text-xs leading-5 font-semibold rounded-full bg-blue-100 text-blue-800">
                              {call.status}
                            </span>
                          )}
                        </td>
                      </tr>
                    ))}
                  </tbody>
                </table>
                <div className="p-4 border-t border-gray-200 flex justify-between items-center">
                  <div className="text-sm text-gray-500">Showing 1 to 4 of 175 results</div>
                  <div className="flex space-x-2">
                    <button className="px-3 py-1 border border-gray-300 rounded-lg text-sm">Previous</button>
                    <button className="px-3 py-1 border border-gray-300 rounded-lg text-sm bg-blue-600 text-white">1</button>
                    <button className="px-3 py-1 border border-gray-300 rounded-lg text-sm">2</button>
                    <button className="px-3 py-1 border border-gray-300 rounded-lg text-sm">3</button>
                    <button className="px-3 py-1 border border-gray-300 rounded-lg text-sm">Next</button>
                  </div>
                </div>
              </div>
            </div>
          )}
          {activeDashboardTab === 'insights' && (
            <div className="p-6 space-y-6">
              <div className="flex justify-between items-center">
                <h1 className="text-2xl font-bold text-gray-800">Contact Insights</h1>
                <div className="flex items-center">
                  <span className="text-sm text-gray-500 mr-2">Insights for</span>
                  <select
                    value={selectedTimeframe}
                    onChange={(e) => setSelectedTimeframe(e.target.value)}
                    className="pl-3 pr-8 py-2 border border-gray-300 rounded-lg focus:outline-none focus:ring-2 focus:ring-blue-500"
                  >
                    <option value="last7Days">Last 7 Days</option>
                    <option value="last30Days">Last 30 Days</option>
                    <option value="last6Months">Last 6 Months</option>
                    <option value="allTime">All Time</option>
                  </select>
                  <ChevronDown className="w-4 h-4 ml-1 text-gray-500" />
                </div>
              </div>
              <div className="grid grid-cols-1 md:grid-cols-3 gap-6">
                <div className="col-span-2 bg-white border border-gray-200 rounded-xl p-6">
                  <h2 className="text-lg font-semibold text-gray-900 mb-4">Avg Success Rate</h2>
                  <div className="flex items-center mb-4">
                    <div className="flex items-center mr-4">
                      <span className="w-3 h-3 bg-blue-500 rounded-full mr-2"></span>
                      <span className="text-sm text-gray-600">85% Primary Info</span>
                    </div>
                    <div className="flex items-center mr-4">
                      <span className="w-3 h-3 bg-orange-500 rounded-full mr-2"></span>
                      <span className="text-sm text-gray-600">85% Required Info</span>
                    </div>
                    <div className="flex items-center">
                      <span className="w-3 h-3 bg-yellow-500 rounded-full mr-2"></span>
                      <span className="text-sm text-gray-600">85% Complete Calls</span>
                    </div>
                  </div>
                  <div className="h-64 flex items-center justify-center">
                    <div className="text-center">
                      <div className="w-48 h-48 rounded-full border-8 border-blue-100 flex items-center justify-center mx-auto mb-4">
                        <div className="w-36 h-36 rounded-full border-8 border-blue-600 flex items-center justify-center">
                          <span className="text-2xl font-bold text-blue-600">85%</span>
                        </div>
                      </div>
                      <p className="text-gray-500">Average Success Rate</p>
                    </div>
                  </div>
                </div>
                <div className="bg-white border border-gray-200 rounded-xl p-6">
                  <h2 className="text-lg font-semibold text-gray-900 mb-4">Top Contacts</h2>
                  <div className="space-y-3">
                    {topContacts.map(contact => (
                      <div key={contact.id} className="border border-gray-200 rounded-lg p-3 hover:shadow-md transition-shadow">
                        <div className="flex justify-between items-start mb-1">
                          <h3 className="font-medium text-gray-900">{contact.name} | {contact.phone}</h3>
                          <span className="text-xs bg-purple-100 text-purple-800 px-2 py-1 rounded">CS Check</span>
                        </div>
                        <div className="flex items-center text-xs text-gray-500">
                          <span className="flex items-center mr-3">
                            <AlertCircle className="w-3 h-3 mr-1 text-yellow-500" />
                            {contact.percentage}% avg
                          </span>
                          <span>{contact.calls} calls</span>
                        </div>
                        <div className="mt-2 flex items-center">
                          {[1, 2, 3, 4, 5].map((star) => (
                            <Star
                              key={star}
                              className={`w-3 h-3 ${star <= Math.floor(contact.rating) ? 'text-yellow-400 fill-yellow-400' :
                                star === Math.ceil(contact.rating) && contact.rating % 1 !== 0 ?
                                  'text-yellow-400 fill-none' : 'text-gray-300 fill-none'}`}
                            />
                          ))}
                        </div>
                      </div>
                    ))}
                  </div>
                </div>
              </div>
              <div className="bg-white border border-gray-200 rounded-xl overflow-hidden">
                <div className="p-4 border-b border-gray-200 flex justify-between items-center">
                  <div className="flex space-x-4">
                    <div className="flex-1 max-w-xs">
                      <select className="w-full px-3 py-2 border border-gray-300 rounded-lg focus:outline-none focus:ring-2 focus:ring-blue-500">
                        <option>Contact #</option>
                        <option>Contact Name</option>
                        <option>Last Called</option>
                      </select>
                    </div>
                    <div className="flex-1">
                      <div className="relative">
                        <input
                          type="text"
                          placeholder="Search contacts..."
                          className="w-full px-3 py-2 border border-gray-300 rounded-lg focus:outline-none focus:ring-2 focus:ring-blue-500 pl-10"
                        />
                        <Search className="w-4 h-4 absolute left-3 top-1/2 transform -translate-y-1/2 text-gray-400" />
                      </div>
                    </div>
                    <button className="flex items-center px-4 py-2 border border-gray-300 rounded-lg hover:bg-gray-50">
                      <Filter className="w-4 h-4 mr-2" /> Filter
                    </button>
                  </div>
                </div>
                <div className="p-4 border-b border-gray-200">
                  <div className="flex justify-between items-center">
                    <div>6 contacts total <span className="text-blue-600 ml-1">Select All</span></div>
                  </div>
                </div>
                <table className="min-w-full divide-y divide-gray-200">
                  <thead className="bg-gray-50">
                    <tr>
                      {['Contact Name', 'Call Type / Goal', 'Contact #', 'Calls', 'Last Call Date', 'Completed via Smart Review', 'Primary Info', 'Required Info', 'Complete Calls'].map((header) => (
                        <th key={header} className="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">
                          {header}
                        </th>
                      ))}
                    </tr>
                  </thead>
                  <tbody className="bg-white divide-y divide-gray-200">
                    {topContacts.map((contact) => (
                      <tr key={contact.id} className="hover:bg-gray-50">
                        <td className="px-6 py-4 whitespace-nowrap text-sm font-medium text-gray-900">{contact.name} | {contact.phone}</td>
                        <td className="px-6 py-4 whitespace-nowrap text-sm text-gray-500">
                          <div className="flex items-center">
                            <span className="bg-purple-100 text-purple-800 px-1.5 py-0.5 rounded mr-1">Claim Status (IVR)</span>
                            <span>CS Check</span>
                          </div>
                        </td>
                        <td className="px-6 py-4 whitespace-nowrap text-sm text-gray-500">{contact.phone}</td>
                        <td className="px-6 py-4 whitespace-nowrap text-sm text-gray-500">{contact.calls}</td>
                        <td className="px-6 py-4 whitespace-nowrap text-sm text-gray-500">12/20/2025</td>
                        <td className="px-6 py-4 whitespace-nowrap text-sm text-gray-500">
                          <span className="text-blue-600">0%</span>
                        </td>
                        <td className="px-6 py-4 whitespace-nowrap text-sm text-gray-500 flex justify-center">
                          <span className="w-3 h-3 bg-blue-500 rounded-full"></span>
                        </td>
                        <td className="px-6 py-4 whitespace-nowrap text-sm text-gray-500 flex justify-center">
                          <span className="w-3 h-3 bg-orange-500 rounded-full"></span>
                        </td>
                        <td className="px-6 py-4 whitespace-nowrap text-sm text-gray-500 flex justify-center">
                          <span className="w-3 h-3 bg-yellow-500 rounded-full"></span>
                        </td>
                      </tr>
                    ))}
                  </tbody>
                </table>
              </div>
            </div>
          )}
          {activeDashboardTab === 'templates' && renderTemplates()}
        </div>
      </main>

      {/* Modals */}
      {isModalOpen && renderCallTypeModal()}
    </div>
  );
};

export default App;
