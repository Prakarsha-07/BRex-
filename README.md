<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Brex - Smart Business Banking</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@300;400;500;600;700&display=swap" rel="stylesheet">
    <style>
        :root {
            --brex-primary: #6E3AFF;
            --brex-secondary: #170F2D;
            --brex-accent: #00D395;
            --brex-light: #F7F9FF;
        }
        
        body {
            font-family: 'Inter', sans-serif;
            background-color: var(--brex-light);
            color: var(--brex-secondary);
        }
        
        .brex-gradient {
            background: linear-gradient(135deg, var(--brex-primary) 0%, #8C4AFF 100%);
        }
        
        .card-gradient {
            background: linear-gradient(135deg, #170F2D 0%, #2D1A5B 100%);
        }
        
        .currency-chip {
            transition: all 0.2s ease;
        }
        
        .currency-chip.active {
            background-color: var(--brex-primary);
            color: white;
        }
        
        .ai-message {
            max-height: 0;
            opacity: 0;
            transition: max-height 0.3s ease, opacity 0.3s ease;
            overflow: hidden;
        }
        
        .ai-message.visible {
            max-height: 200px;
            opacity: 1;
            margin-top: 10px;
        }
        
        .card-security {
            transition: all 0.3s ease;
        }
        
        @media (max-width: 768px) {
            .feature-grid {
                grid-template-columns: 1fr;
            }
        }

        .search-results {
            position: absolute;
            width: 100%;
            z-index: 50;
            background: white;
            box-shadow: 0 4px 6px -1px rgba(0, 0, 0, 0.1);
            border-radius: 0 0 0.5rem 0.5rem;
            max-height: 300px;
            overflow-y: auto;
            display: none;
        }
    </style>
</head>
<body class="min-h-screen">
    <header class="brex-gradient text-white py-6 px-4 md:px-8 lg:px-16">
        <div class="container mx-auto">
            <div class="flex justify-between items-center mb-6">
                <div class="flex items-center space-x-2">
                    <div class="w-10 h-10 rounded-lg bg-white flex items-center justify-center">
                        <svg xmlns="http://www.w3.org/2000/svg" class="h-6 w-6 text-purple-600" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 8c-1.657 0-3 .895-3 2s1.343 2 3 2 3 .895 3 2-1.343 2-3 2m0-8c1.11 0 2.08.402 2.599 1M12 8V7m0 1v8m0 0v1m0-1c-1.11 0-2.08-.402-2.599-1M21 12a9 9 0 11-18 0 9 9 0 0118 0z" />
                        </svg>
                    </div>
                    <h1 class="text-xl md:text-2xl font-bold">Brex</h1>
                </div>
                <nav class="hidden md:block">
                    <ul class="flex space-x-6">
                        <li><a href="#" class="font-medium hover:text-gray-200">Products</a></li>
                        <li><a href="#" class="font-medium hover:text-gray-200">Solutions</a></li>
                        <li><a href="#" class="font-medium hover:text-gray-200">Pricing</a></li>
                        <li><a href="#" class="font-medium hover:text-gray-200">About</a></li>
                    </ul>
                </nav>
                <button class="bg-white text-purple-700 px-4 py-2 rounded-lg font-medium hover:bg-gray-100 md:hidden">
                    Menu
                </button>
            </div>

            <!-- AI Copilot Search Box -->
            <div class="relative max-w-3xl mx-auto">
                <div class="relative">
                    <input type="text" id="copilot-input" 
                           placeholder="Ask your AI copilot about finances, transactions, or business insights..." 
                           class="w-full p-4 pl-12 rounded-lg text-gray-800 focus:outline-none focus:ring-2 focus:ring-purple-500">
                    <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5 text-gray-500 absolute left-4 top-4" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                        <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M21 21l-6-6m2-5a7 7 0 11-14 0 7 7 0 0114 0z" />
                    </svg>
                    <button id="ask-copilot" class="absolute right-2 top-2 bg-purple-700 text-white p-2 rounded-lg hover:bg-purple-800">
                        Ask AI
                    </button>
                </div>
                <div id="copilot-results" class="search-results p-4 hidden">
                    <div class="ai-message text-sm bg-purple-50 text-purple-800 p-3 rounded-lg mb-2">
                        Welcome to Brex AI Copilot! I can help you with:
                        <ul class="list-disc pl-5 mt-2">
                            <li>Expense analysis</li>
                            <li>Budget recommendations</li>
                            <li>Transaction insights</li>
                        </ul>
                    </div>
                    <div id="copilot-suggestions" class="text-sm"></div>
                </div>
            </div>
        </div>
    </header>

    <main class="container mx-auto py-8 px-4 md:px-8 lg:px-16">
        <section class="mb-12">
            <div class="flex flex-col md:flex-row justify-between items-start md:items-end mb-8">
                <div>
                    <h2 class="text-3xl md:text-4xl font-bold mb-2">Your Business Dashboard</h2>
                    <p class="text-gray-600">AI-powered financial management for your business</p>
                </div>
                <button class="brex-gradient text-white px-6 py-3 rounded-lg font-medium hover:bg-purple-700 mt-4 md:mt-0">
                    Upgrade Account
                </button>
            </div>

            <div class="grid feature-grid gap-6 grid-cols-1 md:grid-cols-2 lg:grid-cols-2">
                <!-- Smart Virtual Card -->
                <div class="bg-white rounded-xl shadow-lg p-6">
                    <div class="flex items-center mb-6">
                        <div class="bg-purple-100 p-3 rounded-xl mr-4">
                            <svg xmlns="http://www.w3.org/2000/svg" class="h-6 w-6 text-purple-600" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M3 10h18M7 15h1m4 0h1m-7 4h12a3 3 0 003-3V8a3 3 0 00-3-3H6a3 3 0 00-3 3v8a3 3 0 003 3z" />
                            </svg>
                        </div>
                        <h3 class="text-xl font-semibold">Smart Virtual Card</h3>
                    </div>
                    
                    <div class="card-gradient rounded-xl p-6 text-white mb-6">
                        <div class="flex justify-between items-start mb-8">
                            <div>
                                <p class="text-sm text-gray-300 mb-1">Business Card</p>
                                <p class="font-medium">Brex Corporate</p>
                            </div>
                            <button id="toggle-card" class="text-xs bg-white bg-opacity-20 px-2 py-1 rounded-full flex items-center">
                                <span id="card-status">Active</span>
                                <svg xmlns="http://www.w3.org/2000/svg" class="h-4 w-4 ml-1" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                                    <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M19 9l-7 7-7-7" />
                                </svg>
                            </button>
                        </div>
                        
                        <div class="flex items-center mb-6">
                            <div class="w-8 h-8 bg-purple-400 rounded-full mr-3"></div>
                            <div class="w-8 h-8 bg-pink-400 rounded-full mr-3 opacity-50"></div>
                            <div class="text-xl tracking-widest">••••  ••••  ••••  4242</div>
                        </div>
                        
                        <div class="flex justify-between">
                            <div>
                                <p class="text-xs text-gray-300 mb-1">Card Holder</p>
                                <p class="text-sm">Your Business Name</p>
                            </div>
                            <div>
                                <p class="text-xs text-gray-300 mb-1">Expires</p>
                                <p class="text-sm">12/25</p>
                            </div>
                            <div>
                                <p class="text-xs text-gray-300 mb-1">CVV</p>
                                <p class="text-sm">•••</p>
                            </div>
                        </div>
                    </div>
                    
                    <div class="space-y-4">
                        <div class="flex justify-between items-center card-security" id="online-payments">
                            <div class="flex items-center">
                                <div class="w-8 h-8 rounded-full bg-green-100 flex items-center justify-center mr-3">
                                    <svg xmlns="http://www.w3.org/2000/svg" class="h-4 w-4 text-green-600" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                                        <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M5 13l4 4L19 7" />
                                    </svg>
                                </div>
                                <p>Online Payments</p>
                            </div>
                            <label class="relative inline-flex items-center cursor-pointer">
                                <input type="checkbox" checked class="sr-only peer">
                                <div class="w-11 h-6 bg-green-500 peer-focus:outline-none rounded-full peer peer-checked:after:translate-x-full peer-checked:after:border-white after:content-[''] after:absolute after:top-[2px] after:left-[2px] after:bg-white after:border-gray-300 after:border after:rounded-full after:h-5 after:w-5 after:transition-all"></div>
                            </label>
                        </div>
                        
                        <div class="flex justify-between items-center card-security" id="contactless">
                            <div class="flex items-center">
                                <div class="w-8 h-8 rounded-full bg-red-100 flex items-center justify-center mr-3">
                                    <svg xmlns="http://www.w3.org/2000/svg" class="h-4 w-4 text-red-600" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                                        <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M6 18L18 6M6 6l12 12" />
                                    </svg>
                                </div>
                                <p>Contactless Payments</p>
                            </div>
                            <label class="relative inline-flex items-center cursor-pointer">
                                <input type="checkbox" class="sr-only peer">
                                <div class="w-11 h-6 bg-gray-200 peer-focus:outline-none rounded-full peer peer-checked:after:translate-x-full peer-checked:after:border-white after:content-[''] after:absolute after:top-[2px] after:left-[2px] after:bg-white after:border-gray-300 after:border after:rounded-full after:h-5 after:w-5 after:transition-all"></div>
                            </label>
                        </div>
                        
                        <div class="mt-6">
                            <button id="generate-card" class="w-full border border-purple-500 text-purple-600 p-2 rounded-lg font-medium hover:bg-purple-50">
                                Generate New Card Number
                            </button>
                        </div>
                    </div>
                </div>

                <!-- Real-Time Multi-Currency Wallet -->
                <div class="bg-white rounded-xl shadow-lg p-6">
                    <div class="flex items-center mb-6">
                        <div class="bg-purple-100 p-3 rounded-xl mr-4">
                            <svg xmlns="http://www.w3.org/2000/svg" class="h-6 w-6 text-purple-600" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 8c-1.657 0-3 .895-3 2s1.343 2 3 2 3 .895 3 2-1.343 2-3 2m0-8c1.11 0 2.08.402 2.599 1M12 8V7m0 1v8m0 0v1m0-1c-1.11 0-2.08-.402-2.599-1M21 12a9 9 0 11-18 0 9 9 0 0118 0z" />
                            </svg>
                        </div>
                        <h3 class="text-xl font-semibold">Multi-Currency Wallet</h3>
                    </div>
                    
                    <div class="flex space-x-2 mb-6 overflow-x-auto pb-2">
                        <button class="currency-chip active px-4 py-2 rounded-full bg-purple-600 text-white text-sm">USD</button>
                        <button class="currency-chip px-4 py-2 rounded-full bg-gray-100 text-gray-700 text-sm">EUR</button>
                        <button class="currency-chip px-4 py-2 rounded-full bg-gray-100 text-gray-700 text-sm">GBP</button>
                        <button class="currency-chip px-4 py-2 rounded-full bg-gray-100 text-gray-700 text-sm">JPY</button>
                        <button class="currency-chip px-4 py-2 rounded-full bg-gray-100 text-gray-700 text-sm">CAD</button>
                    </div>
                    
                    <div class="mb-6">
                        <div class="flex justify-between items-center mb-2">
                            <p class="text-gray-600">Available Balance</p>
                            <p class="text-2xl font-bold">$24,589.42</p>
                        </div>
                        <div class="flex justify-between items-center text-sm text-gray-500 mb-4">
                            <p>≈ €21,876.58</p>
                            <p>≈ £18,927.34</p>
                        </div>
                        <div class="h-2 bg-gray-200 rounded-full overflow-hidden">
                            <div class="h-full bg-green-500 rounded-full" style="width: 72%"></div>
                        </div>
                    </div>
                    
                    <div class="grid grid-cols-2 gap-4 mb-6">
                        <div class="bg-purple-50 p-4 rounded-lg">
                            <p class="text-xs text-purple-600 mb-1">Exchange Rate</p>
                            <p class="font-medium">1 USD = 0.89 EUR</p>
                        </div>
                        <div class="bg-blue-50 p-4 rounded-lg">
                            <p class="text-xs text-blue-600 mb-1">Exchange Rate</p>
                            <p class="font-medium">1 USD = 0.77 GBP</p>
                        </div>
                    </div>
                    
                    <div class="space-y-3">
                        <button class="w-full bg-green-50 text-green-700 p-3 rounded-lg font-medium flex items-center justify-center hover:bg-green-100">
                            <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5 mr-2" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 8v4l3 3m6-3a9 9 0 11-18 0 9 9 0 0118 0z" />
                            </svg>
                            Real-Time Rate Lock
                        </button>
                        <button class="w-full bg-white border border-gray-200 text-gray-700 p-3 rounded-lg font-medium flex items-center justify-center hover:bg-gray-50">
                            <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5 mr-2" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M7 16a4 4 0 01-.88-7.903A5 5 0 1115.9 6L16 6a5 5 0 011 9.9M9 19l3 3m0 0l3-3m-3 3V10" />
                            </svg>
                            Convert Currency
                        </button>
                    </div>
                </div>
            </div>
        </section>

        <section class="mb-12">
            <h3 class="text-xl font-semibold mb-6">Recent Transactions</h3>
            <div class="bg-white rounded-xl shadow-lg overflow-hidden">
                <div class="grid grid-cols-12 bg-gray-100 p-4 font-medium text-gray-600 text-sm">
                    <div class="col-span-5">Description</div>
                    <div class="col-span-2">Type</div>
                    <div class="col-span-2">Amount</div>
                    <div class="col-span-2">Date</div>
                    <div class="col-span-1 text-right">Receipt</div>
                </div>
                
                <div class="divide-y divide-gray-100">
                    <div class="grid grid-cols-12 p-4 hover:bg-gray-50">
                        <div class="col-span-5 flex items-center">
                            <div class="w-10 h-10 rounded-lg bg-purple-100 flex items-center justify-center mr-3">
                                <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5 text-purple-600" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                                    <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M19 21V5a2 2 0 00-2-2H7a2 2 0 00-2 2v16m14 0h2m-2 0h-5m-9 0H3m2 0h5M9 7h1m-1 4h1m4-4h1m-1 4h1m-5 10v-5a1 1 0 011-1h2a1 1 0 011 1v5m-4 0h4" />
                                </svg>
                            </div>
                            <div>
                                <p class="font-medium">Amazon Web Services</p>
                                <p class="text-sm text-gray-500">Cloud Computing</p>
                            </div>
                        </div>
                        <div class="col-span-2 flex items-center">
                            <span class="bg-purple-100 text-purple-800 px-2 py-1 rounded text-xs">Recurring</span>
                        </div>
                        <div class="col-span-2 flex items-center font-medium">
                            $1,285.00
                        </div>
                        <div class="col-span-2 flex items-center text-sm text-gray-500">
                            Jun 15, 2023
                        </div>
                        <div class="col-span-1 flex items-center justify-end">
                            <a href="#" class="text-purple-600 hover:text-purple-800">
                                <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                                    <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M7 21h10a2 2 0 002-2V9.414a1 1 0 00-.293-.707l-5.414-5.414A1 1 0 0012.586 3H7a2 2 0 00-2 2v14a2 2 0 002 2z" />
                                </svg>
                            </a>
                        </div>
                    </div>
                    
                    <div class="grid grid-cols-12 p-4 hover:bg-gray-50">
                        <div class="col-span-5 flex items-center">
                            <div class="w-10 h-10 rounded-lg bg-green-100 flex items-center justify-center mr-3">
                                <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5 text-green-600" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                                    <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M9 14l6-6m-5.5.5h.01m4.99 5h.01M19 21V5a2 2 0 00-2-2H7a2 2 0 00-2 2v16m14 0h2m-2 0h-5m-9 0H3m2 0h5M9 7h1m-1 4h1m4-4h1m-1 4h1m-5 10v-5a1 1 0 011-1h2a1 1 0 011 1v5m-4 0h4" />
                                </svg>
                            </div>
                            <div>
                                <p class="font-medium">G Suite Business</p>
                                <p class="text-sm text-gray-500">Productivity</p>
                            </div>
                        </div>
                        <div class="col-span-2 flex items-center">
                            <span class="bg-green-100 text-green-800 px-2 py-1 rounded text-xs">Subscription</span>
                        </div>
                        <div class="col-span-2 flex items-center font-medium">
                            $72.00
                        </div>
                        <div class="col-span-2 flex items-center text-sm text-gray-500">
                            Jun 14, 2023
                        </div>
                        <div class="col-span-1 flex items-center justify-end">
                            <a href="#" class="text-purple-600 hover:text-purple-800">
                                <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                                    <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M7 21h10a2 2 0 002-2V9.414a1 1 0 00-.293-.707l-5.414-5.414A1 1 0 0012.586 3H7a2 2 0 00-2 2v14a2 2 0 002 2z" />
                                </svg>
                            </a>
                        </div>
                    </div>
                    
                    <div class="grid grid-cols-12 p-4 hover:bg-gray-50">
                        <div class="col-span-5 flex items-center">
                            <div class="w-10 h-10 rounded-lg bg-blue-100 flex items-center justify-center mr-3">
                                <img src="https://storage.googleapis.com/workspace-0f70711f-8b4e-4d94-86f1-2a93ccde5887/image/ab65d5d7-3942-47a7-826e-36f46baf7e4a.png" alt="Slack logo in bright purple with white text" class="w-5 h-5" />
                            </div>
                            <div>
                                <p class="font-medium">Slack Technologies</p>
                                <p class="text-sm text-gray-500">Communication</p>
                            </div>
                        </div>
                        <div class="col-span-2 flex items-center">
                            <span class="bg-blue-100 text-blue-800 px-2 py-1 rounded text-xs">Software</span>
                        </div>
                        <div class="col-span-2 flex items-center font-medium">
                            $12.00
                        </div>
                        <div class="col-span-2 flex items-center text-sm text-gray-500">
                            Jun 13, 2023
                        </div>
                        <div class="col-span-1 flex items-center justify-end">
                            <a href="#" class="text-purple-600 hover:text-purple-800">
                                <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                                    <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M7 21h10a2 2 0 002-2V9.414a1 1 0 00-.293-.707l-5.414-5.414A1 1 0 0012.586 3H7a2 2 0 00-2 2v14a2 2 0 002 2z" />
                                </svg>
                            </a>
                        </div>
                    </div>
                </div>
            </div>
        </section>
    </main>

    <footer class="bg-white border-t border-gray-200 py-8 px-4 md:px-8 lg:px-16">
        <div class="container mx-auto">
            <div class="flex flex-col md:flex-row justify-between items-center">
                <div class="mb-4 md:mb-0">
                    <div class="flex items-center space-x-2">
                        <div class="w-8 h-8 rounded-lg bg-purple-600 flex items-center justify-center">
                            <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5 text-white" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 8c-1.657 0-3 .895-3 2s1.343 2 3 2 3 .895 3 2-1.343 2-3 2m0-8c1.11 0 2.08.402 2.599 1M12 8V7m0 1v8m0 0v1m0-1c-1.11 0-2.08-.402-2.599-1M21 12a9 9 0 11-18 0 9 9 0 0118 0z" />
                            </svg>
                        </div>
                        <h2 class="text-xl font-bold">Brex</h2>
                    </div>
                    <p class="text-sm text-gray-500 mt-2">The smarter way to manage business finances.</p>
                </div>
                
                <div class="grid grid-cols-2 md:grid-cols-4 gap-8">
                    <div>
                        <h4 class="text-sm font-semibold text-gray-900 uppercase tracking-wider mb-3">Product</h4>
                        <ul class="space-y-2">
                            <li><a href="#" class="text-sm text-gray-600 hover:text-purple-600">Features</a></li>
                            <li><a href="#" class="text-sm text-gray-600 hover:text-purple-600">Pricing</a></li>
                            <li><a href="#" class="text-sm text-gray-600 hover:text-purple-600">Security</a></li>
                            <li><a href="#" class="text-sm text-gray-600 hover:text-purple-600">Integrations</a></li>
                        </ul>
                    </div>
                    
                    <div>
                        <h4 class="text-sm font-semibold text-gray-900 uppercase tracking-wider mb-3">Resources</h4>
                        <ul class="space-y-2">
                            <li><a href="#" class="text-sm text-gray-600 hover:text-purple-600">Documentation</a></li>
                            <li><a href="#" class="text-sm text-gray-600 hover:text-purple-600">API</a></li>
                            <li><a href="#" class="text-sm text-gray-600 hover:text-purple-600">Support</a></li>
                            <li><a href="#" class="text-sm text-gray-600 hover:text-purple-600">Status</a></li>
                        </ul>
                    </div>
                    
                    <div>
                        <h4 class="text-sm font-semibold text-gray-900 uppercase tracking-wider mb-3">Company</h4>
                        <ul class="space-y-2">
                            <li><a href="#" class="text-sm text-gray-600 hover:text-purple-600">About</a></li>
                            <li><a href="#" class="text-sm text-gray-600 hover:text-purple-600">Careers</a></li>
                            <li><a href="#" class="text-sm text-gray-600 hover:text-purple-600">Blog</a></li>
                            <li><a href="#" class="text-sm text-gray-600 hover:text-purple-600">Press</a></li>
                        </ul>
                    </div>
                    
                    <div>
                        <h4 class="text-sm font-semibold text-gray-900 uppercase tracking-wider mb-3">Legal</h4>
                        <ul class="space-y-2">
                            <li><a href="#" class="text-sm text-gray-600 hover:text-purple-600">Privacy</a></li>
                            <li><a href="#" class="text-sm text-gray-600 hover:text-purple-600">Terms
