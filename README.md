<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Factor Fun!</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <!-- React Libraries -->
    <script src="https://unpkg.com/react@17/umd/react.development.js"></script>
    <script src="https://unpkg.com/react-dom@17/umd/react-dom.development.js"></script>
    <!-- Babel to transpile JSX -->
    <script src="https://unpkg.com/@babel/standalone/babel.min.js"></script>
</head>
<body>
    <div id="root"></div>

    <script type="text/babel">
        const { useState } = React;

        // Custom Modal Component to replace alert()
        const Modal = ({ message, onClose }) => {
            if (!message) return null;
            return (
                <div className="fixed inset-0 bg-black bg-opacity-50 flex items-center justify-center z-50">
                    <div className="bg-white rounded-lg shadow-lg p-6 w-11/12 max-w-sm text-center">
                        <p className="text-gray-800 text-lg mb-4">{message}</p>
                        <button
                            onClick={onClose}
                            className="bg-blue-500 text-white px-6 py-2 rounded-lg hover:bg-blue-600 focus:outline-none focus:ring-2 focus:ring-blue-400"
                        >
                            Got it!
                        </button>
                    </div>
                </div>
            );
        };

        const FactorPairsLesson = () => {
          const [currentStep, setCurrentStep] = useState(0);
          const [completedSteps, setCompletedSteps] = useState(new Set());
          const [answers, setAnswers] = useState({});
          const [showHints, setShowHints] = useState({});
          const [score, setScore] = useState(0);
          const [currentTab, setCurrentTab] = useState('factors');
          const [modalMessage, setModalMessage] = useState('');

          const checkAnswer8 = () => {
            const pair1_a = parseInt(answers['8_pair1_a'] || 0);
            const pair1_b = parseInt(answers['8_pair1_b'] || 0);
            const pair2_a = parseInt(answers['8_pair2_a'] || 0);
            const pair2_b = parseInt(answers['8_pair2_b'] || 0);

            const validPairs = [[1, 8], [8, 1], [2, 4], [4, 2]];
            
            const userPair1 = [pair1_a, pair1_b];
            const userPair2 = [pair2_a, pair2_b];

            const isValid1 = validPairs.some(([a, b]) => (userPair1[0] === a && userPair1[1] === b));
            const isValid2 = validPairs.some(([a, b]) => (userPair2[0] === a && userPair2[1] === b));
            const areDifferent = (userPair1.slice().sort().join(',') !== userPair2.slice().sort().join(','));


            if (isValid1 && isValid2 && areDifferent) {
              setCompletedSteps(prev => new Set([...prev, 'guided_practice_8']));
              setScore(prev => prev + 10);
              setModalMessage("🎉 Great job! You found the factor pairs for 8!");
            } else {
              setModalMessage("Not quite right. Remember: 1×8=8 and 2×4=8. Try again!");
            }
          };

          const checkAnswer15 = () => {
            const pair1_a = parseInt(answers['15_pair1_a'] || 0);
            const pair1_b = parseInt(answers['15_pair1_b'] || 0);
            const pair2_a = parseInt(answers['15_pair2_a'] || 0);
            const pair2_b = parseInt(answers['15_pair2_b'] || 0);

            const validPairs = [[1, 15], [15, 1], [3, 5], [5, 3]];
            
            const userPair1 = [pair1_a, pair1_b];
            const userPair2 = [pair2_a, pair2_b];

            const isValid1 = validPairs.some(([a, b]) => userPair1[0] === a && userPair1[1] === b);
            const isValid2 = validPairs.some(([a, b]) => userPair2[0] === a && userPair2[1] === b);
            const areDifferent = (userPair1.slice().sort().join(',') !== userPair2.slice().sort().join(','));

            if (isValid1 && isValid2 && areDifferent) {
              setCompletedSteps(prev => new Set([...prev, 'guided_practice_15']));
              setScore(prev => prev + 15);
              setModalMessage("🌟 Excellent! You found the factor pairs for 15!");
            } else {
              setModalMessage("Keep trying! Remember: 1×15=15 and 3×5=15.");
            }
          };

          const checkGardenProblem = () => {
            const arrangements = [
              [parseInt(answers['garden_1_rows'] || 0), parseInt(answers['garden_1_cols'] || 0)],
              [parseInt(answers['garden_2_rows'] || 0), parseInt(answers['garden_2_cols'] || 0)],
              [parseInt(answers['garden_3_rows'] || 0), parseInt(answers['garden_3_cols'] || 0)]
            ].map(p => p.slice().sort().join(','));

            const validArrangements = [[1,12], [2,6], [3,4]].map(p => p.slice().sort().join(','));
            
            let correctCount = 0;
            const uniqueUserArrangements = [...new Set(arrangements)];

            uniqueUserArrangements.forEach(arrangement => {
                if (validArrangements.includes(arrangement)) {
                    correctCount++;
                }
            });
            
            if (correctCount === 3) {
              setCompletedSteps(prev => new Set([...prev, 'word_problem']));
              setScore(prev => prev + 20);
              setModalMessage("🏆 Outstanding! You solved the garden problem!");
            } else {
              setModalMessage(`You found ${correctCount} out of 3 unique arrangements. The arrangements are 1×12, 2×6, and 3×4 (or their reverses).`);
            }
          };

          const checkProductAnswers = () => {
            const product1 = parseInt(answers['product_1'] || 0);
            const product2 = parseInt(answers['product_2'] || 0);
            const product3 = parseInt(answers['product_3'] || 0);
            
            let correctCount = 0;
            if (product1 === 12) correctCount++;
            if (product2 === 12) correctCount++;
            if (product3 === 9) correctCount++;
            
            if (correctCount === 3) {
              setCompletedSteps(prev => new Set([...prev, 'product_practice']));
              setScore(prev => prev + 15);
              setModalMessage("🎉 Perfect! You found all the products correctly!");
            } else {
              setModalMessage(`You got ${correctCount} out of 3 correct. Remember: 2×6=12, 4×3=12, 1×9=9`);
            }
          };

          const nextStep = () => {
            const maxSteps = currentTab === 'factors' ? factorSteps.length : productSteps.length;
            if (currentStep < maxSteps - 1) {
              setCurrentStep(currentStep + 1);
            }
          };

          const prevStep = () => {
            if (currentStep > 0) {
              setCurrentStep(currentStep - 1);
            }
          };

          const resetLesson = () => {
            setCurrentStep(0);
            setCompletedSteps(new Set());
            setAnswers({});
            setShowHints({});
            setScore(0);
          };

          const factorSteps = [
            {
              id: 'intro',
              title: 'What Are Factors?',
              content: (
                <div className="space-y-4">
                  <div className="bg-blue-50 p-4 rounded-lg border-2 border-blue-200">
                    <h3 className="text-lg font-bold text-blue-800 mb-2">🧩 Factors are numbers that multiply together!</h3>
                    <p className="text-blue-700">When we multiply two numbers together, those numbers are called factors.</p>
                  </div>
                  
                  <div className="bg-green-50 p-4 rounded-lg border-2 border-green-200">
                    <h4 className="font-bold text-green-800 mb-2">Example: Finding factors of 6</h4>
                    <div className="space-y-2 text-center">
                      <div className="bg-white p-3 rounded border-2 border-green-300">
                        <span className="text-red-600 font-bold text-lg">1</span>
                        <span className="mx-2">×</span>
                        <span className="text-red-600 font-bold text-lg">6</span>
                        <span className="mx-2">=</span>
                        <span className="text-blue-600 font-bold text-lg">6</span>
                      </div>
                      <div className="bg-white p-3 rounded border-2 border-green-300">
                        <span className="text-red-600 font-bold text-lg">2</span>
                        <span className="mx-2">×</span>
                        <span className="text-red-600 font-bold text-lg">3</span>
                        <span className="mx-2">=</span>
                        <span className="text-blue-600 font-bold text-lg">6</span>
                      </div>
                    </div>
                    <p className="mt-3 text-green-700 font-semibold">So the factors of 6 are: 1, 2, 3, and 6</p>
                  </div>

                  <div className="bg-yellow-50 p-4 rounded-lg border-2 border-yellow-200">
                    <h4 className="font-bold text-yellow-800 mb-2">🎯 Factor Pairs</h4>
                    <p className="text-yellow-700 mb-2">A factor pair is two factors that multiply together to make our number!</p>
                    <div className="text-center space-y-1">
                      <div className="bg-yellow-200 px-3 py-2 rounded inline-block mr-2">1 and 6 are a factor pair</div>
                      <div className="bg-yellow-200 px-3 py-2 rounded inline-block">2 and 3 are a factor pair</div>
                    </div>
                  </div>
                </div>
              )
            },
            {
              id: 'guided_practice_8',
              title: 'Your Turn: Find Factors of 8',
              content: (
                <div className="space-y-4">
                  <div className="bg-orange-50 p-4 rounded-lg border-2 border-orange-200">
                    <h3 className="text-lg font-bold text-orange-800 mb-4">🎯 Find all factor pairs for 8</h3>
                    
                    <div className="space-y-3">
                      <div className="bg-white p-3 rounded border-2 border-gray-300">
                        <label className="block font-semibold mb-2">Factor pair 1:</label>
                        <div className="flex items-center space-x-2">
                          <input
                            type="number"
                            min="1"
                            max="8"
                            className="w-16 p-2 border rounded text-center"
                            value={answers['8_pair1_a'] || ''}
                            onChange={(e) => setAnswers(prev => ({...prev, '8_pair1_a': e.target.value}))}
                          />
                          <span className="text-xl">×</span>
                          <input
                            type="number"
                            min="1"
                            max="8"
                            className="w-16 p-2 border rounded text-center"
                            value={answers['8_pair1_b'] || ''}
                            onChange={(e) => setAnswers(prev => ({...prev, '8_pair1_b': e.target.value}))}
                          />
                          <span className="text-xl">=</span>
                          <span className="text-xl font-bold">8</span>
                        </div>
                      </div>
                      
                      <div className="bg-white p-3 rounded border-2 border-gray-300">
                        <label className="block font-semibold mb-2">Factor pair 2:</label>
                        <div className="flex items-center space-x-2">
                          <input
                            type="number"
                            min="1"
                            max="8"
                            className="w-16 p-2 border rounded text-center"
                            value={answers['8_pair2_a'] || ''}
                            onChange={(e) => setAnswers(prev => ({...prev, '8_pair2_a': e.target.value}))}
                          />
                          <span className="text-xl">×</span>
                          <input
                            type="number"
                            min="1"
                            max="8"
                            className="w-16 p-2 border rounded text-center"
                            value={answers['8_pair2_b'] || ''}
                            onChange={(e) => setAnswers(prev => ({...prev, '8_pair2_b': e.target.value}))}
                          />
                          <span className="text-xl">=</span>
                          <span className="text-xl font-bold">8</span>
                        </div>
                      </div>
                    </div>
                    
                    <div className="mt-4 flex space-x-2">
                      <button
                        onClick={() => setShowHints(prev => ({...prev, '8': !prev['8']}))}
                        className="px-4 py-2 bg-yellow-500 text-white rounded hover:bg-yellow-600"
                      >
                        {showHints['8'] ? 'Hide' : 'Show'} Hint
                      </button>
                      <button
                        onClick={checkAnswer8}
                        className="px-4 py-2 bg-green-500 text-white rounded hover:bg-green-600"
                      >
                        Check My Work
                      </button>
                    </div>
                    
                    {showHints['8'] && (
                      <div className="mt-3 p-3 bg-yellow-100 border-2 border-yellow-300 rounded">
                        <p className="text-yellow-800">💡 <strong>Hint:</strong> Try 1 × ?, then 2 × ?. What numbers work?</p>
                      </div>
                    )}
                  </div>
                </div>
              )
            },
            {
              id: 'guided_practice_15',
              title: 'Challenge: Find Factors of 15',
              content: (
                <div className="space-y-4">
                  <div className="bg-teal-50 p-4 rounded-lg border-2 border-teal-200">
                    <h3 className="text-lg font-bold text-teal-800 mb-4">🌟 Find all factor pairs for 15</h3>
                    
                    <div className="space-y-3">
                      <div className="bg-white p-3 rounded border-2 border-gray-300">
                        <label className="block font-semibold mb-2">Factor pair 1:</label>
                        <div className="flex items-center space-x-2">
                          <input
                            type="number"
                            min="1"
                            max="15"
                            className="w-16 p-2 border rounded text-center"
                            value={answers['15_pair1_a'] || ''}
                            onChange={(e) => setAnswers(prev => ({...prev, '15_pair1_a': e.target.value}))}
                          />
                          <span className="text-xl">×</span>
                          <input
                            type="number"
                            min="1"
                            max="15"
                            className="w-16 p-2 border rounded text-center"
                            value={answers['15_pair1_b'] || ''}
                            onChange={(e) => setAnswers(prev => ({...prev, '15_pair1_b': e.target.value}))}
                          />
                          <span className="text-xl">=</span>
                          <span className="text-xl font-bold">15</span>
                        </div>
                      </div>
                      
                      <div className="bg-white p-3 rounded border-2 border-gray-300">
                        <label className="block font-semibold mb-2">Factor pair 2:</label>
                        <div className="flex items-center space-x-2">
                          <input
                            type="number"
                            min="1"
                            max="15"
                            className="w-16 p-2 border rounded text-center"
                            value={answers['15_pair2_a'] || ''}
                            onChange={(e) => setAnswers(prev => ({...prev, '15_pair2_a': e.target.value}))}
                          />
                          <span className="text-xl">×</span>
                          <input
                            type="number"
                            min="1"
                            max="15"
                            className="w-16 p-2 border rounded text-center"
                            value={answers['15_pair2_b'] || ''}
                            onChange={(e) => setAnswers(prev => ({...prev, '15_pair2_b': e.target.value}))}
                          />
                          <span className="text-xl">=</span>
                          <span className="text-xl font-bold">15</span>
                        </div>
                      </div>
                    </div>
                    
                    <div className="mt-4 flex space-x-2">
                      <button
                        onClick={() => setShowHints(prev => ({...prev, '15': !prev['15']}))}
                        className="px-4 py-2 bg-yellow-500 text-white rounded hover:bg-yellow-600"
                      >
                        {showHints['15'] ? 'Hide' : 'Show'} Hint
                      </button>
                      <button
                        onClick={checkAnswer15}
                        className="px-4 py-2 bg-green-500 text-white rounded hover:bg-green-600"
                      >
                        Check My Work
                      </button>
                    </div>
                    
                    {showHints['15'] && (
                      <div className="mt-3 p-3 bg-yellow-100 border-2 border-yellow-300 rounded">
                        <p className="text-yellow-800">💡 <strong>Hint:</strong> 15 = 3 × ? and 15 = 1 × ?</p>
                      </div>
                    )}
                  </div>
                </div>
              )
            },
            {
              id: 'word_problem',
              title: 'Real-World Problem',
              content: (
                <div className="space-y-4">
                  <div className="bg-pink-50 p-4 rounded-lg border-2 border-pink-200">
                    <h3 className="text-lg font-bold text-pink-800 mb-4">🍎 Garden Problem</h3>
                    <p className="text-pink-700 mb-4">
                      Maria has 12 tomato plants. She wants to arrange them in a rectangular garden. 
                      How many different rectangular arrangements can she make?
                    </p>
                    
                    <div className="bg-white p-4 rounded border-2 border-gray-300">
                      <p className="font-semibold mb-2">List all possible arrangements:</p>
                      <div className="space-y-2">
                        <div className="flex items-center space-x-2">
                          <span>Arrangement 1:</span>
                          <input
                            type="number"
                            min="1"
                            className="w-16 p-1 border rounded text-center"
                            value={answers['garden_1_rows'] || ''}
                            onChange={(e) => setAnswers(prev => ({...prev, 'garden_1_rows': e.target.value}))}
                          />
                          <span>rows ×</span>
                          <input
                            type="number"
                            min="1"
                            className="w-16 p-1 border rounded text-center"
                            value={answers['garden_1_cols'] || ''}
                            onChange={(e) => setAnswers(prev => ({...prev, 'garden_1_cols': e.target.value}))}
                          />
                          <span>columns</span>
                        </div>
                        
                        <div className="flex items-center space-x-2">
                          <span>Arrangement 2:</span>
                          <input
                            type="number"
                            min="1"
                            className="w-16 p-1 border rounded text-center"
                            value={answers['garden_2_rows'] || ''}
                            onChange={(e) => setAnswers(prev => ({...prev, 'garden_2_rows': e.target.value}))}
                          />
                          <span>rows ×</span>
                          <input
                            type="number"
                            min="1"
                            className="w-16 p-1 border rounded text-center"
                            value={answers['garden_2_cols'] || ''}
                            onChange={(e) => setAnswers(prev => ({...prev, 'garden_2_cols': e.target.value}))}
                          />
                          <span>columns</span>
                        </div>
                        
                        <div className="flex items-center space-x-2">
                          <span>Arrangement 3:</span>
                          <input
                            type="number"
                            min="1"
                            className="w-16 p-1 border rounded text-center"
                            value={answers['garden_3_rows'] || ''}
                            onChange={(e) => setAnswers(prev => ({...prev, 'garden_3_rows': e.target.value}))}
                          />
                          <span>rows ×</span>
                          <input
                            type="number"
                            min="1"
                            className="w-16 p-1 border rounded text-center"
                            value={answers['garden_3_cols'] || ''}
                            onChange={(e) => setAnswers(prev => ({...prev, 'garden_3_cols': e.target.value}))}
                          />
                          <span>columns</span>
                        </div>
                      </div>
                    </div>
                    
                    <div className="mt-4">
                      <button
                        onClick={checkGardenProblem}
                        className="px-4 py-2 bg-green-500 text-white rounded hover:bg-green-600"
                      >
                        Check My Answer
                      </button>
                    </div>
                  </div>
                </div>
              )
            }
          ];

          const productSteps = [
            {
              id: 'product_intro',
              title: 'What Is a Product?',
              content: (
                <div className="space-y-4">
                  <div className="bg-purple-50 p-4 rounded-lg border-2 border-purple-200">
                    <h3 className="text-lg font-bold text-purple-800 mb-2">🎲 Product = The Answer When We Multiply!</h3>
                    <p className="text-purple-700 text-lg">The product is the number we get when we multiply two numbers together.</p>
                  </div>
                  
                  <div className="bg-pink-50 p-4 rounded-lg border-2 border-pink-200">
                    <h4 className="font-bold text-pink-800 mb-3">Let's See Some Examples:</h4>
                    <div className="space-y-3">
                      <div className="bg-white p-3 rounded border-2 border-pink-300 text-center">
                        <span className="text-red-600 font-bold text-xl">3</span>
                        <span className="mx-3 text-gray-600 text-xl">×</span>
                        <span className="text-red-600 font-bold text-xl">4</span>
                        <span className="mx-3 text-gray-600 text-xl">=</span>
                        <span className="text-pink-600 font-bold text-xl bg-pink-200 px-2 py-1 rounded">12</span>
                        <p className="mt-2 text-pink-700">The product is 12!</p>
                      </div>
                    </div>
                  </div>

                  <div className="bg-orange-50 p-4 rounded-lg border-2 border-orange-200">
                    <h4 className="font-bold text-orange-800 mb-2">🍎 Real Life Example</h4>
                    <p className="text-orange-700 mb-2">If you have 3 bags with 4 apples in each bag:</p>
                    <div className="bg-white p-3 rounded border text-center">
                      <p className="text-lg">3 bags × 4 apples = <span className="font-bold text-orange-600 bg-orange-200 px-2 py-1 rounded">12 apples total</span></p>
                      <p className="mt-2 text-orange-600">12 is the product!</p>
                    </div>
                  </div>
                </div>
              )
            },
            {
              id: 'product_practice',
              title: 'Practice Finding Products',
              content: (
                <div className="space-y-4">
                  <div className="bg-teal-50 p-4 rounded-lg border-2 border-teal-200">
                    <h3 className="text-lg font-bold text-teal-800 mb-4">🎯 Find the Products</h3>
                    
                    <div className="space-y-4">
                      <div className="bg-white p-4 rounded border-2 border-gray-300">
                        <div className="flex items-center justify-center space-x-3 text-xl">
                          <span className="font-bold text-red-600">2</span>
                          <span>×</span>
                          <span className="font-bold text-red-600">6</span>
                          <span>=</span>
                          <input
                            type="number"
                            min="1"
                            className="w-20 p-2 border rounded text-center text-xl font-bold"
                            value={answers['product_1'] || ''}
                            onChange={(e) => setAnswers(prev => ({...prev, 'product_1': e.target.value}))}
                          />
                        </div>
                      </div>

                      <div className="bg-white p-4 rounded border-2 border-gray-300">
                        <div className="flex items-center justify-center space-x-3 text-xl">
                          <span className="font-bold text-red-600">4</span>
                          <span>×</span>
                          <span className="font-bold text-red-600">3</span>
                          <span>=</span>
                          <input
                            type="number"
                            min="1"
                            className="w-20 p-2 border rounded text-center text-xl font-bold"
                            value={answers['product_2'] || ''}
                            onChange={(e) => setAnswers(prev => ({...prev, 'product_2': e.target.value}))}
                          />
                        </div>
                      </div>

                      <div className="bg-white p-4 rounded border-2 border-gray-300">
                        <div className="flex items-center justify-center space-x-3 text-xl">
                          <span className="font-bold text-red-600">1</span>
                          <span>×</span>
                          <span className="font-bold text-red-600">9</span>
                          <span>=</span>
                          <input
                            type="number"
                            min="1"
                            className="w-20 p-2 border rounded text-center text-xl font-bold"
                            value={answers['product_3'] || ''}
                            onChange={(e) => setAnswers(prev => ({...prev, 'product_3': e.target.value}))}
                          />
                        </div>
                      </div>
                    </div>

                    <div className="mt-4">
                      <button
                        onClick={checkProductAnswers}
                        className="px-4 py-2 bg-green-500 text-white rounded hover:bg-green-600"
                      >
                        Check My Products
                      </button>
                    </div>
                  </div>
                </div>
              )
            }
          ];

          const currentSteps = currentTab === 'factors' ? factorSteps : productSteps;

          return (
            <div className="max-w-4xl mx-auto p-2 sm:p-6 bg-gray-50 min-h-screen">
                <Modal message={modalMessage} onClose={() => setModalMessage('')} />
                <div className="bg-white rounded-lg shadow-lg p-4 sm:p-6">
                    {/* Header */}
                    <div className="mb-6">
                    <h1 className="text-3xl font-bold text-center text-blue-800 mb-2">
                        🧮 Understanding Factors and Products
                    </h1>
                    <p className="text-center text-gray-600 mb-4">
                        Learn about numbers that multiply together!
                    </p>
                    
                    {/* Tab Navigation */}
                    <div className="flex justify-center mb-4">
                        <div className="bg-gray-100 p-1 rounded-lg">
                        <button
                            onClick={() => {setCurrentTab('factors'); setCurrentStep(0);}}
                            className={`px-6 py-2 rounded-md font-semibold ${
                            currentTab === 'factors'
                                ? 'bg-blue-500 text-white'
                                : 'text-blue-600 hover:bg-blue-50'
                            }`}
                        >
                            🧩 Factors
                        </button>
                        <button
                            onClick={() => {setCurrentTab('products'); setCurrentStep(0);}}
                            className={`px-6 py-2 rounded-md font-semibold ${
                            currentTab === 'products'
                                ? 'bg-purple-500 text-white'
                                : 'text-purple-600 hover:bg-purple-50'
                            }`}
                        >
                            🎲 Products
                        </button>
                        </div>
                    </div>
                    
                    <div className="flex justify-between items-center bg-blue-50 p-3 rounded-lg">
                        <div className="flex items-center space-x-2">
                            <span className="text-yellow-500">⭐</span>
                            <span className="font-semibold">Score: {score}</span>
                        </div>
                        <div className="text-sm text-blue-700">
                        Step {currentStep + 1} of {currentSteps.length}
                        </div>
                        <button
                        onClick={resetLesson}
                        className="flex items-center space-x-1 text-blue-600 hover:text-blue-800"
                        >
                            <span>🔄</span>
                            <span>Reset</span>
                        </button>
                    </div>
                    </div>

                    {/* Progress Bar */}
                    <div className="mb-6">
                    <div className="flex justify-between items-center mb-2">
                        {currentSteps.map((step, index) => (
                        <div key={step.id} className="flex items-center flex-1">
                            <button
                            onClick={() => setCurrentStep(index)}
                            className={`w-8 h-8 rounded-full flex items-center justify-center transition-colors duration-300 ${
                                completedSteps.has(step.id)
                                ? 'bg-green-500 text-white'
                                : index === currentStep
                                ? currentTab === 'factors' ? 'bg-blue-500 text-white' : 'bg-purple-500 text-white'
                                : 'bg-gray-300 text-gray-600'
                            }`}
                            >
                            {completedSteps.has(step.id) ? (
                                <span>✅</span>
                            ) : (
                                index + 1
                            )}
                            </button>
                            {index < currentSteps.length - 1 && (
                            <div className={`h-1 flex-1 transition-colors duration-300 ${
                                completedSteps.has(step.id) ? 'bg-green-500' : 'bg-gray-300'
                            }`} />
                            )}
                        </div>
                        ))}
                    </div>
                    </div>

                    {/* Current Step Content */}
                    <div className="mb-6 min-h-[300px]">
                    <h2 className="text-2xl font-bold text-gray-800 mb-4">
                        {currentSteps[currentStep].title}
                    </h2>
                    {currentSteps[currentStep].content}
                    </div>

                    {/* Navigation */}
                    <div className="flex justify-between">
                    <button
                        onClick={prevStep}
                        disabled={currentStep === 0}
                        className={`px-6 py-2 rounded transition-colors duration-300 ${
                        currentStep === 0
                            ? 'bg-gray-300 text-gray-500 cursor-not-allowed'
                            : currentTab === 'factors' ? 'bg-blue-500 text-white hover:bg-blue-600' : 'bg-purple-500 text-white hover:bg-purple-600'
                        }`}
                    >
                        ← Previous
                    </button>
                    
                    <button
                        onClick={nextStep}
                        disabled={currentStep === currentSteps.length - 1}
                        className={`px-6 py-2 rounded transition-colors duration-300 ${
                        currentStep === currentSteps.length - 1
                            ? 'bg-gray-300 text-gray-500 cursor-not-allowed'
                            : currentTab === 'factors' ? 'bg-blue-500 text-white hover:bg-blue-600' : 'bg-purple-500 text-white hover:bg-purple-600'
                        }`}
                    >
                        Next →
                    </button>
                    </div>

                    {/* Completion Badge */}
                    {completedSteps.size >= 4 && (
                    <div className="mt-6 text-center">
                        <div className="inline-flex items-center space-x-2 bg-green-100 text-green-800 px-4 py-2 rounded-full">
                        <span>🏆</span>
                        <span className="font-bold">Great Progress! Keep Learning!</span>
                        </div>
                    </div>
                    )}
                </div>
            </div>
          );
        };

        ReactDOM.render(<FactorPairsLesson />, document.getElementById('root'));
    </script>
</body>
</html>
