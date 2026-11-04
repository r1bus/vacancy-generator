import React, { useState, useEffect, useRef } from 'react';
import { 
  Mic, Square, Sparkles, ChevronLeft, ChevronRight, 
  Copy, Check, Loader2 
} from 'lucide-react';

// --- Данные для шагов (единая сущность: кандидат) ---
const STEPS = [
  {
    section: "ЗАДАЧИ",
    question: "Каковы ежедневные задачи кандидата?",
    mockAnswer: "Проектирование пользовательских интерфейсов для мобильного приложения, десктопной и мобильной версий сайта. Создание интерактивных прототипов, проработка пользовательских сценариев и интерфейсных анимаций."
  },
  {
    section: "ПРОЕКТ",
    question: "Над каким проектом предстоит работать первые полгода?",
    mockAnswer: "Работа над флагманской цифровой платформой, создание новых продуктовых сценариев и улучшение ключевых пользовательских воронок."
  },
  {
    section: "РАЗВИТИЕ",
    question: "Каковы будущие проекты и перспективы развития?",
    mockAnswer: "Масштабирование экосистемы продуктов, развитие дизайн-системы и внедрение современных инструментов в процессы проектирования интерфейсов."
  },
  {
    section: "ТРЕБОВАНИЯ",
    question: "Какими инструментами и навыками должен владеть кандидат?",
    mockAnswer: "Опыт работы в продуктовом дизайне от 5 лет. Уверенное владение Figma, нейросетями для графики, понимание принципов веб-верстки и опыт UX-исследований."
  }
];

// Типографический микро-плагин: перенос висячих предлогов через неразрывный пробел
function formatTypography(text) {
  if (!text) return '';
  return text.replace(/(\s|^)([а-яА-ЯёЁ]{1,2})\s+/g, '$1$2\u00A0');
}

const LOADING_TEXTS = [
  "Убираем лишнее",
  "Улучшаем текст",
  "Формируем вакансию"
];

export default function App() {
  const [currentStep, setCurrentStep] = useState(0);
  const [isRecording, setIsRecording] = useState(false);
  const [answers, setAnswers] = useState(["", "", "", ""]);
  
  const [isLoading, setIsLoading] = useState(false);
  const [loadingTextIndex, setLoadingTextIndex] = useState(0);
  const [showResult, setShowResult] = useState(false);
  
  const [isCopied, setIsCopied] = useState(false);
  const [copiedSection, setCopiedSection] = useState(null);
  const [hasError, setHasError] = useState(false);
  
  // Текстовые блоки по разделам
  const [dutiesText, setDutiesText] = useState(
    "• Проектирование пользовательских интерфейсов для мобильного приложения, десктопной и мобильной версий сайта.\n• Создание интерактивных прототипов, проработка пользовательских сценариев и интерфейсных анимаций.\n• Участие в планировании и проведении UX-исследований, анализ пользовательского поведения.\n• Генерация и проверка продуктовых гипотез на основе аналитики.\n• Работа с бизнес-требованиями и техническими ограничениями, презентация и согласование результатов."
  );

  const [requirementsText, setRequirementsText] = useState(
    "• Опыт работы в продуктовом дизайне от 5 лет, преимущественно с мобильными приложениями в B2C-продуктах.\n• Уверенное владение Figma, опыт использования нейросетей для создания и обработки графики.\n• Опыт взаимодействия с продуктовыми и бизнес-аналитиками, UX-исследователями и командами разработки.\n• Понимание принципов веб-верстки и особенностей мобильной разработки."
  );

  const [conditionsText, setConditionsText] = useState(
    "• Работа в офисе с 11:00 до 19:00.\n• Крутой опыт в большой и быстрорастущей компании с сильным управлением и развитым маркетингом.\n• Ежегодная индексация зарплаты, компенсация питания, премии по результатам работы.\n• ДМС расширенный со стоматологией после ИС.\n• Предоставление современной техники с учетом индивидуальных пожеланий."
  );

  const recordingIntervalRef = useRef(null);
  const charIndexRef = useRef(0);
  const textareaRef = useRef(null);

  const toggleRecording = () => {
    if (isRecording) {
      setIsRecording(false);
      clearInterval(recordingIntervalRef.current);
    } else {
      setIsRecording(true);
      if (hasError) setHasError(false);
      const targetText = STEPS[currentStep].mockAnswer;
      charIndexRef.current = answers[currentStep].length;
      
      recordingIntervalRef.current = setInterval(() => {
        if (charIndexRef.current < targetText.length) {
          const nextChars = targetText.slice(charIndexRef.current, charIndexRef.current + 3);
          setAnswers(prev => {
            const newAnswers = [...prev];
            newAnswers[currentStep] = prev[currentStep] + nextChars;
            return newAnswers;
          });
          charIndexRef.current += 3;
        } else {
          setIsRecording(false);
          clearInterval(recordingIntervalRef.current);
        }
      }, 50);
    }
  };

  useEffect(() => {
    return () => clearInterval(recordingIntervalRef.current);
  }, []);

  const handleNext = () => {
    if (isRecording) toggleRecording();
    
    if (!answers[currentStep].trim()) {
      setHasError(true);
      return;
    }

    setHasError(false);
    if (currentStep < 3) {
      setCurrentStep(prev => prev + 1);
    } else {
      startGeneration();
    }
  };

  const handlePrev = () => {
    if (isRecording) toggleRecording();
    if (hasError) setHasError(false);
    if (currentStep > 0) {
      setCurrentStep(prev => prev - 1);
    }
  };

  const handleTextChange = (e, index = currentStep) => {
    if (hasError) setHasError(false);
    const newAnswers = [...answers];
    newAnswers[index] = e.target.value;
    setAnswers(newAnswers);
  };

  const startGeneration = () => {
    setIsLoading(true);
    setLoadingTextIndex(0);
    
    const textInterval = setInterval(() => {
      setLoadingTextIndex(prev => {
        if (prev < LOADING_TEXTS.length - 1) return prev + 1;
        return prev;
      });
    }, 1200);

    setTimeout(() => {
      clearInterval(textInterval);
      setIsLoading(false);
      setShowResult(true);
    }, 6000);
  };

  const copyToClipboard = () => {
    const resultText = `
UX/UI дизайнер

ОБЯЗАННОСТИ:
${dutiesText}

ТРЕБОВАНИЯ:
${requirementsText}

УСЛОВИЯ:
${conditionsText}
    `.trim();

    const handleCopySuccess = () => {
      setIsCopied(true);
      setTimeout(() => setIsCopied(false), 2000);
    };

    if (navigator.clipboard && window.isSecureContext) {
      navigator.clipboard.writeText(resultText)
        .then(handleCopySuccess)
        .catch(() => fallbackCopyTextToClipboard(resultText, handleCopySuccess));
    } else {
      fallbackCopyTextToClipboard(resultText, handleCopySuccess);
    }
  };

  const copySection = (sectionName, text) => {
    const handleSectionCopySuccess = () => {
      setCopiedSection(sectionName);
      setTimeout(() => setCopiedSection(null), 2000);
    };

    if (navigator.clipboard && window.isSecureContext) {
      navigator.clipboard.writeText(text)
        .then(handleSectionCopySuccess)
        .catch(() => fallbackCopyTextToClipboard(text, handleSectionCopySuccess));
    } else {
      fallbackCopyTextToClipboard(text, handleSectionCopySuccess);
    }
  };

  const fallbackCopyTextToClipboard = (text, onSuccess) => {
    try {
      const textArea = document.createElement("textarea");
      textArea.value = text;
      textArea.style.top = "0";
      textArea.style.left = "0";
      textArea.style.position = "fixed";
      document.body.appendChild(textArea);
      textArea.focus();
      textArea.select();
      if (document.execCommand('copy')) {
        onSuccess();
      }
      document.body.removeChild(textArea);
    } catch (err) {
      console.error('Fallback copy failed', err);
    }
  };

  useEffect(() => {
    if (textareaRef.current && !showResult) {
      textareaRef.current.style.height = 'auto';
      textareaRef.current.style.height = textareaRef.current.scrollHeight + 'px';
    }
  }, [answers, currentStep, showResult]);

  const handleTextareaInput = (e) => {
    e.target.style.height = 'auto';
    e.target.style.height = `${e.target.scrollHeight + 4}px`;
  };

  const useAutoResize = (value) => {
    const ref = useRef(null);
    useEffect(() => {
      const timer = setTimeout(() => {
        if (ref.current) {
          ref.current.style.height = 'auto';
          ref.current.style.height = `${ref.current.scrollHeight + 4}px`;
        }
      }, 50);
      return () => clearTimeout(timer);
    }, [value, showResult]);
    return ref;
  };

  const dutiesRef = useAutoResize(dutiesText);
  const requirementsRef = useAutoResize(requirementsText);
  const conditionsRef = useAutoResize(conditionsText);

  return (
    <div className="min-h-screen bg-gray-50 text-gray-900 font-sans selection:bg-blue-100 selection:text-blue-900">
      
      <header className="bg-white/85 backdrop-blur-md sticky top-0 z-10 border-b border-gray-100">
        <div className="max-w-3xl mx-auto px-6 h-16 flex items-center justify-between">
          <div className="flex items-center gap-2.5 cursor-default">
            <div className="relative w-6 h-6 flex items-center justify-center">
              <div className="absolute w-4 h-4 bg-[#007AFF] rounded-full mix-blend-multiply opacity-80 -translate-x-1.5"></div>
              <div className="absolute w-4 h-4 bg-cyan-400 rounded-full mix-blend-multiply opacity-80 translate-x-1.5"></div>
            </div>
            <span className="font-semibold text-lg tracking-tight">Создание вакансии</span>
          </div>
          {!showResult && (
            <div className="flex items-center">
              <span className="text-sm font-medium text-gray-400 tracking-wide">
                Шаг {currentStep + 1} из 4
              </span>
            </div>
          )}
        </div>
      </header>

      <main className="max-w-3xl mx-auto px-6 py-8 relative">
        
        {!showResult && (
          <div className="animate-in fade-in slide-in-from-bottom-4 duration-500">
            <div className="mb-8 text-center sm:text-left flex flex-col gap-2">
              <span className="text-xs font-bold text-gray-400 tracking-widest uppercase block">
                {STEPS[currentStep].section}
              </span>
              <h1 className="text-3xl sm:text-4xl font-bold text-gray-900 leading-tight">
                {formatTypography(STEPS[currentStep].question)}
              </h1>
            </div>

            <div className={`bg-white rounded-[2rem] pt-14 pb-8 px-6 sm:pt-16 sm:pb-10 sm:px-12 shadow-[0_2px_20px_rgb(0,0,0,0.03)] border transition-all duration-200 flex flex-col items-center w-full relative ${
              hasError ? 'border-red-300 ring-4 ring-red-50 animate-shake' : 'border-gray-100'
            }`}>
              
              <button 
                onClick={toggleRecording}
                className={`relative flex items-center justify-center rounded-full transition-colors duration-300 group z-10 ${
                  isRecording 
                    ? 'w-16 h-16 bg-[#007AFF] text-white shadow-[0_0_0_8px_rgba(0,122,255,0.2)] animate-pulse' 
                    : 'w-16 h-16 bg-gray-100 text-gray-600 hover:bg-gray-200 transition-colors'
                }`}
              >
                {isRecording ? <Square className="w-8 h-8 fill-current" /> : <Mic className="w-8 h-8" />}
                
                <div className="absolute bottom-full mb-3 opacity-0 group-hover:opacity-100 transition-opacity duration-200 pointer-events-none">
                  <span className="bg-[#E5E7EB] text-gray-900 text-xs font-medium py-1.5 px-3 rounded-lg whitespace-nowrap shadow-sm">
                    {isRecording ? "Остановить голосовой ввод" : "Начать голосовой ввод"}
                  </span>
                </div>
              </button>

              <div className="w-full mt-6 relative max-w-2xl">
                <textarea
                  ref={textareaRef}
                  value={answers[currentStep]}
                  onChange={handleTextChange}
                  placeholder="Говорите или введите текст..."
                  rows={1}
                  className="w-full min-h-[44px] text-lg sm:text-xl text-gray-800 bg-transparent outline-none border-none resize-none focus:outline-none focus:ring-0 placeholder:text-gray-300 transition-all leading-relaxed overflow-hidden"
                />
              </div>

              {hasError && (
                <div className="w-full mt-4 text-left animate-in fade-in duration-200">
                  <span className="text-xs font-medium text-red-500 bg-red-50 px-3 py-1.5 rounded-lg border border-red-100 inline-block">
                    Надиктуйте ответ голосом или введите текстом
                  </span>
                </div>
              )}
            </div>

            <div className="mt-6 flex items-center justify-between px-2">
              <button 
                onClick={handlePrev}
                className={`flex items-center justify-center gap-2 pl-4 pr-6 py-3 rounded-xl font-semibold text-gray-600 bg-gray-100 hover:bg-gray-200 transition-colors ${currentStep === 0 ? 'invisible' : ''}`}
              >
                <ChevronLeft className="w-5 h-5" />
                <span className="leading-none pb-[2px]">Назад</span>
              </button>

              <button 
                onClick={handleNext}
                className={`flex items-center justify-center gap-2 py-3 rounded-xl font-semibold text-white shadow-sm transition-colors bg-[#007AFF] hover:bg-blue-600 ${
                  currentStep === 3 ? 'pl-6 pr-5' : 'pl-6 pr-4'
                }`}
              >
                {currentStep === 3 ? (
                  <>
                    <span className="leading-none pb-[2px]">Создать вакансию</span>
                    <Sparkles className="w-5 h-5" />
                  </>
                ) : (
                  <>
                    <span className="leading-none pb-[2px]">Далее</span>
                    <ChevronRight className="w-5 h-5" />
                  </>
                )}
              </button>
            </div>
          </div>
        )}

        {showResult && !isLoading && (
          <div className="animate-in slide-in-from-bottom-8 fade-in duration-700">
            
            <div className="bg-white rounded-3xl p-6 sm:p-10 shadow-[0_8px_30px_rgb(0,0,0,0.04)] border border-gray-100 space-y-8">
              
              <div>
                <h1 className="text-3xl sm:text-4xl font-bold text-gray-900">
                  UX/UI дизайнер
                </h1>
              </div>

              {/* Текстовые гибкие блоки с кнопками копирования каждого раздела */}
              <div className="space-y-6">
                
                {/* Обязанности */}
                <div className="flex flex-col gap-1.5">
                  <div className="flex items-center justify-between">
                    <h3 className="text-lg font-bold text-gray-900">Обязанности</h3>
                    <button
                      onClick={() => copySection('duties', dutiesText)}
                      title="Копировать обязанности"
                      className="px-2.5 py-1.5 rounded-lg text-gray-500 hover:text-gray-900 hover:bg-gray-100 transition-all active:scale-95 flex items-center gap-1.5 text-xs font-medium"
                    >
                      {copiedSection === 'duties' ? (
                        <>
                          <Check className="w-4 h-4 text-green-600" />
                          <span className="text-green-600">Скопировано</span>
                        </>
                      ) : (
                        <>
                          <Copy className="w-4 h-4 text-gray-400" />
                          <span>Копировать</span>
                        </>
                      )}
                    </button>
                  </div>
                  <textarea
                    value={dutiesText}
                    onChange={(e) => setDutiesText(e.target.value)}
                    onInput={handleTextareaInput}
                    ref={dutiesRef}
                    rows={1}
                    className="w-full text-base text-gray-800 bg-gray-50 border border-gray-200 rounded-xl p-4 focus:ring-2 focus:ring-[#007AFF] focus:border-transparent transition-all leading-relaxed resize-none"
                  />
                </div>

                {/* Требования */}
                <div className="flex flex-col gap-1.5">
                  <div className="flex items-center justify-between">
                    <h3 className="text-lg font-bold text-gray-900">Требования</h3>
                    <button
                      onClick={() => copySection('requirements', requirementsText)}
                      title="Копировать требования"
                      className="px-2.5 py-1.5 rounded-lg text-gray-500 hover:text-gray-900 hover:bg-gray-100 transition-all active:scale-95 flex items-center gap-1.5 text-xs font-medium"
                    >
                      {copiedSection === 'requirements' ? (
                        <>
                          <Check className="w-4 h-4 text-green-600" />
                          <span className="text-green-600">Скопировано</span>
                        </>
                      ) : (
                        <>
                          <Copy className="w-4 h-4 text-gray-400" />
                          <span>Копировать</span>
                        </>
                      )}
                    </button>
                  </div>
                  <textarea
                    value={requirementsText}
                    onChange={(e) => setRequirementsText(e.target.value)}
                    onInput={handleTextareaInput}
                    ref={requirementsRef}
                    rows={1}
                    className="w-full text-base text-gray-800 bg-gray-50 border border-gray-200 rounded-xl p-4 focus:ring-2 focus:ring-[#007AFF] focus:border-transparent transition-all leading-relaxed resize-none"
                  />
                </div>

                {/* Условия */}
                <div className="flex flex-col gap-1.5">
                  <div className="flex items-center justify-between">
                    <h3 className="text-lg font-bold text-gray-900">Условия</h3>
                    <button
                      onClick={() => copySection('conditions', conditionsText)}
                      title="Копировать условия"
                      className="px-2.5 py-1.5 rounded-lg text-gray-500 hover:text-gray-900 hover:bg-gray-100 transition-all active:scale-95 flex items-center gap-1.5 text-xs font-medium"
                    >
                      {copiedSection === 'conditions' ? (
                        <>
                          <Check className="w-4 h-4 text-green-600" />
                          <span className="text-green-600">Скопировано</span>
                        </>
                      ) : (
                        <>
                          <Copy className="w-4 h-4 text-gray-400" />
                          <span>Копировать</span>
                        </>
                      )}
                    </button>
                  </div>
                  <textarea
                    value={conditionsText}
                    onChange={(e) => setConditionsText(e.target.value)}
                    onInput={handleTextareaInput}
                    ref={conditionsRef}
                    rows={1}
                    className="w-full text-base text-gray-800 bg-gray-50 border border-gray-200 rounded-xl p-4 focus:ring-2 focus:ring-[#007AFF] focus:border-transparent transition-all leading-relaxed resize-none"
                  />
                </div>

              </div>
            </div>

            <div className="sticky bottom-6 mt-8 flex flex-wrap items-center justify-center gap-3 z-25">
              <button 
                onClick={copyToClipboard}
                className={`flex items-center gap-2 px-8 py-4 rounded-full font-semibold text-white shadow-xl transition-all hover:scale-105 active:scale-95 ${
                  isCopied ? 'bg-green-500' : 'bg-[#007AFF] hover:bg-blue-600 shadow-blue-500/20'
                }`}
              >
                {isCopied ? (
                  <>
                    <Check className="w-5 h-5" />
                    Текст скопирован!
                  </>
                ) : (
                  <>
                    <Copy className="w-5 h-5" />
                    Копировать вакансию
                  </>
                )}
              </button>

              <button 
                onClick={() => alert('Вакансия сохранена!')}
                className="flex items-center gap-2 px-6 py-4 rounded-full font-semibold text-gray-700 bg-white border border-gray-200 shadow-lg hover:bg-gray-50 transition-all hover:scale-105 active:scale-95"
              >
                Сохранить вакансию
              </button>
            </div>
            
          </div>
        )}
      </main>

      {isLoading && (
        <div className="fixed inset-0 z-50 flex items-center justify-center bg-white/80 backdrop-blur-lg animate-in fade-in duration-300">
          <div className="flex flex-col items-center gap-5 text-center p-6 max-w-sm">
            <div className="relative">
              <div className="absolute inset-0 bg-blue-500 blur-[40px] opacity-20 rounded-full"></div>
              <Loader2 className="w-14 h-14 text-[#007AFF] animate-spin relative z-10" />
            </div>
            
            <div className="flex flex-col gap-1.5 items-center">
              <div className="h-7 overflow-hidden relative w-full flex justify-center">
                <p 
                  key={loadingTextIndex}
                  className="text-lg font-semibold text-gray-900 animate-in slide-in-from-bottom-4 fade-in duration-300 absolute"
                >
                  {LOADING_TEXTS[loadingTextIndex]}
                </p>
              </div>
              <p className="text-sm text-gray-500">
                Это займет пару секунд
              </p>
            </div>
          </div>
        </div>
      )}

    </div>
  );
}
