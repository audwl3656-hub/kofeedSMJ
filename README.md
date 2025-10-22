import React, { useState, useEffect } from 'react';
import { Share2, Check, X } from 'lucide-react';

// Dataset
const SPECIES = ["양돈","가금","고기소","젖소","수산"];

const PROFILES_COMPOUND = {
  "양돈-포유자돈":{"구리":{"limit":100,"unit":"ppm"},"아연":{"limit":120,"unit":"ppm"},"인":{"limit":0.8,"unit":"%"},"조단백질":{"limit":20,"unit":"%"}},
  "양돈-이유돈":{"구리":{"limit":100,"unit":"ppm"},"아연":{"limit":120,"unit":"ppm"},"인":{"limit":0.8,"unit":"%"},"조단백질":{"limit":18,"unit":"%"}},
  "양돈-육성돈":{"구리":{"limit":60,"unit":"ppm"},"아연":{"limit":90,"unit":"ppm"},"인":{"limit":0.6,"unit":"%"},"조단백질":{"limit":16,"unit":"%"}},
  "양돈-비육돈":{"아연":{"limit":75,"unit":"ppm"},"인":{"limit":0.6,"unit":"%"},"조단백질":{"limit":14,"unit":"%"}},
  "양돈-번식용모돈":{"아연":{"limit":150,"unit":"ppm"},"인":{"limit":0.7,"unit":"%"},"조단백질":{"limit":15,"unit":"%"}},
  "양돈-임신돈":{"조단백질":{"limit":13,"unit":"%"}},
  "양돈-포유돈":{"조단백질":{"limit":19,"unit":"%"}},
  "가금-산란어린병아리":{"인":{"limit":0.7,"unit":"%"},"조단백질":{"limit":21,"unit":"%"}},
  "가금-산란중병아리":{"인":{"limit":0.7,"unit":"%"},"조단백질":{"limit":18,"unit":"%"}},
  "가금-산란큰병아리":{"인":{"limit":0.7,"unit":"%"},"조단백질":{"limit":16,"unit":"%"}},
  "가금-산란전":{"인":{"limit":0.7,"unit":"%"},"조단백질":{"limit":17,"unit":"%"}},
  "가금-산란초기":{"인":{"limit":0.6,"unit":"%"},"조단백질":{"limit":19,"unit":"%"}},
  "가금-산란중기":{"인":{"limit":0.6,"unit":"%"},"조단백질":{"limit":18,"unit":"%"}},
  "가금-산란말기":{"인":{"limit":0.6,"unit":"%"},"조단백질":{"limit":17,"unit":"%"}},
  "가금-산란종계":{"인":{"limit":0.7,"unit":"%"},"조단백질":{"limit":19,"unit":"%"}},
  "가금-육용어린병아리":{"조단백질":{"limit":20,"unit":"%"}},
  "가금-육용중병아리":{"조단백질":{"limit":17,"unit":"%"}},
  "가금-육용종계":{"조단백질":{"limit":16,"unit":"%"}},
  "가금-육계-초기":{"조단백질":{"limit":23,"unit":"%"}},
  "가금-육계-전기":{"조단백질":{"limit":22,"unit":"%"}},
  "가금-육계-후기":{"조단백질":{"limit":20,"unit":"%"}},
  "가금-육용오리전기":{"조단백질":{"limit":21,"unit":"%"}},
  "가금-육용오리후기":{"조단백질":{"limit":19,"unit":"%"}},
  "가금-어린오리":{"조단백질":{"limit":22,"unit":"%"}},
  "가금-육성오리":{"조단백질":{"limit":18,"unit":"%"}},
  "가금-산란오리":{"조단백질":{"limit":20,"unit":"%"}},
  "고기소-어린송아지":{"조단백질":{"limit":24,"unit":"%"}},
  "고기소-육성우":{"조단백질":{"limit":18,"unit":"%"}},
  "고기소-번식우":{"조단백질":{"limit":16,"unit":"%"}},
  "고기소-종모우":{"조단백질":{"limit":17,"unit":"%"}},
  "고기소-임신우":{"조단백질":{"limit":15,"unit":"%"}},
  "고기소-포유우":{"조단백질":{"limit":18,"unit":"%"}},
  "고기소-큰소전기":{"조단백질":{"limit":17,"unit":"%"}},
  "고기소-큰소후기":{"조단백질":{"limit":15,"unit":"%"}},
  "젖소-어린송아지":{"조단백질":{"limit":24,"unit":"%"}},
  "젖소-중송아지":{"조단백질":{"limit":19,"unit":"%"}},
  "젖소-큰송아지":{"조단백질":{"limit":18,"unit":"%"}},
  "젖소-임신우":{"조단백질":{"limit":17,"unit":"%"}},
  "젖소-종모우":{"조단백질":{"limit":17,"unit":"%"}},
  "젖소-비유초기":{"조단백질":{"limit":24,"unit":"%"}},
  "젖소-비유중기":{"조단백질":{"limit":19,"unit":"%"}},
  "젖소-비유말기":{"조단백질":{"limit":18,"unit":"%"}},
  "젖소-건유기":{"조단백질":{"limit":17,"unit":"%"}},
  "젖소-고능력우":{"조단백질":{"limit":22,"unit":"%"}},
  "수산-일반":{"인":{"limit":1.8,"unit":"%"},"셀레늄":{"limit":10,"unit":"ppm"}},
  "수산-큰물고기":{"인":{"limit":1.5,"unit":"%"}},
  "수산-뱀장어":{"인":{"limit":2.7,"unit":"%"}},
  "수산-해수어":{"인":{"limit":2.7,"unit":"%"}},
};

const GROUP_DEFAULTS_COMPOUND = {
  "양돈":{"구리":{"limit":25,"unit":"ppm"}},
  "수산":{"인":{"limit":1.8,"unit":"%"}},
};

const CORE_ANALYTES = ["수분","조단백질","조지방","조섬유","조회분","ADF","NDF","칼슘","인","염분","셀레늄","구리","아연"];
const HAZARD_ANALYTES = ["납(Pb)","불소(F)","비소(As)","수은(Hg)","카드뮴(Cd)","크롬(Cr)","아플라톡신(총)","오크라톡신A","데옥시니발레놀(DON)","제랄레논(ZEA)","푸모니신(B1+B2)","T-2/HT-2","방사능-Cs합계","방사능-I131"];

const FEED_CLASS = {
  COMPOUND: "배합사료",
  SINGLE_PLANT_OR_ANIMAL: "단미사료(식물성/동물성)",
  SINGLE_MINERAL: "단미사료(광물성)",
  SUPPLEMENT: "보조사료",
  HARMFUL: "유해물질"
};

const round2 = (x) => Math.round((x + Number.EPSILON) * 100) / 100;

function toleranceForAnalyte({analyte, reg, feedClass}) {
  const a = (analyte || "").trim().toLowerCase();
  const f = feedClass;
  const rel = (p) => ({type: "relative", pct: p, abs: null});
  const abs = (v) => ({type: "absolute", pct: null, abs: v});
  
  if (a === "moisture" || a === "수분") {
    if (reg < 3) return abs(0.66);
    if (reg < 40) return rel(12);
    return rel(5);
  }
  if (a === "crude protein" || a === "조단백질") {
    if (reg < 10) return rel(10);
    return rel((20 / reg) + 2);
  }
  if (a === "crude fat" || a === "조지방") {
    if (reg < 3) return rel(20);
    if (reg < 20) return rel(10);
    if (reg < 50) return rel(5);
    return rel(3);
  }
  if (a === "crude fiber" || a === "조섬유") {
    if (reg < 2) return rel(20);
    return rel((30 / reg) + 6);
  }
  if (a === "ash" || a === "조회분") {
    if (reg < 2) return rel(20);
    return rel((45 / reg) + 3);
  }
  if (a === "calcium" || a === "칼슘") {
    const pct = reg < 10 ? 12 : 10;
    return rel(pct);
  }
  if (a === "phosphorus" || a === "인") {
    if (reg < 0.5) return abs(0.1);
    return rel((3 / reg) + 8);
  }
  if (a === "salt" || a === "염분" || a === "nacl") {
    if (f === FEED_CLASS.SINGLE_MINERAL) return rel(7);
    if (reg < 1.0) return abs(0.24);
    return rel((15 / reg) + 9);
  }
  if (a === "adf" || a === "ndf") {
    return {type: "none", pct: null, abs: null};
  }
  if (a === "selenium" || a === "셀레늄" || a === "se") return rel(25);
  if (a === "구리" || a === "copper" || a === "cu") return rel(15);
  if (a === "아연" || a === "zinc" || a === "zn") return rel(15);
  
  if (f === FEED_CLASS.HARMFUL) {
    if (reg < 12.5) return rel(20);
    if (reg < 50) return rel(10);
    return rel(5);
  }
  
  return {type: "none", pct: null, abs: null};
}

function computeAllowed({reg, analyte, feedClass}) {
  const rule = toleranceForAnalyte({analyte, reg, feedClass});
  
  if (rule.type === "none") {
    return {status: "NO_RULE", allowedMax: reg, rule};
  }
  
  let tol = 0;
  if (rule.type === "relative") tol = reg * (rule.pct / 100.0);
  if (rule.type === "absolute") tol = rule.abs;
  
  const allowedMax = round2(reg + tol);
  
  return {status: "OK", rule, tol: round2(tol), allowedMax};
}

function evaluate({measured, reg, analyte, feedClass}) {
  const res = computeAllowed({reg, analyte, feedClass});
  const x = round2(measured);
  const pass = x <= res.allowedMax;
  
  return {...res, measured: x, pass};
}

function limitFromHazard16(analyte, group, profile) {
  if (analyte === "납(Pb)") return (group === "수산") ? {limit: 20, unit: "ppm"} : {limit: 10, unit: "ppm"};
  if (analyte === "불소(F)") {
    if (group === "젖소") return {limit: 30, unit: "ppm"};
    if (group === "고기소") return {limit: 50, unit: "ppm"};
    if (group === "양돈") return {limit: 100, unit: "ppm"};
    if (group === "가금") return {limit: 250, unit: "ppm"};
    if (group === "수산") return {limit: 1000, unit: "ppm"};
    return {limit: 300, unit: "ppm"};
  }
  if (analyte === "비소(As)") return (group === "수산") ? {limit: 20, unit: "ppm"} : {limit: 2, unit: "ppm"};
  if (analyte === "수은(Hg)") return {limit: 0.4, unit: "ppm"};
  if (analyte === "카드뮴(Cd)") return (group === "수산") ? {limit: 2, unit: "ppm"} : {limit: 1, unit: "ppm"};
  if (analyte === "크롬(Cr)") return (group === "수산") ? {limit: 100, unit: "ppm"} : null;
  
  if (analyte === "아플라톡신(총)") {
    const young10 = ["양돈-포유자돈","양돈-이유돈","가금-육계-초기","가금-육계-전기","가금-육용오리전기","가금-육용어린병아리","가금-산란어린병아리","고기소-어린송아지","젖소-어린송아지","젖소-비유초기"];
    return young10.includes(profile) ? {limit: 10, unit: "ppb"} : {limit: 20, unit: "ppb"};
  }
  if (analyte === "오크라톡신A") return {limit: 200, unit: "ppb"};
  if (analyte === "데옥시니발레놀(DON)") {
    if (group === "양돈") return {limit: 900, unit: "ppb"};
    const youngR = ["고기소-어린송아지","젖소-어린송아지"];
    if (youngR.includes(profile)) return {limit: 2000, unit: "ppb"};
    return {limit: 5000, unit: "ppb"};
  }
  if (analyte === "제랄레논(ZEA)") {
    const pig100 = ["양돈-포유자돈","양돈-이유돈","양돈-번식용모돈","양돈-임신돈","양돈-포유돈"];
    if (pig100.includes(profile)) return {limit: 100, unit: "ppb"};
    const pig250 = ["양돈-육성돈","양돈-비육돈"];
    if (pig250.includes(profile)) return {limit: 250, unit: "ppb"};
    if (group === "고기소" || group === "젖소") return {limit: 500, unit: "ppb"};
    return {limit: 1000, unit: "ppb"};
  }
  if (analyte === "푸모니신(B1+B2)") {
    if (group === "양돈") return {limit: 5000, unit: "ppb"};
    if (group === "수산") return {limit: 10000, unit: "ppb"};
    const youngR = ["고기소-어린송아지","젖소-어린송아지"];
    if (group === "가금" || youngR.includes(profile)) return {limit: 20000, unit: "ppb"};
    if (group === "고기소" || group === "젖소") return {limit: 50000, unit: "ppb"};
    return {limit: 30000, unit: "ppb"};
  }
  if (analyte === "T-2/HT-2") return {limit: 250, unit: "ppb"};
  if (analyte === "방사능-Cs합계") {
    if (group === "양돈") return {limit: 80, unit: "Bq/kg"};
    if (group === "가금") return {limit: 160, unit: "Bq/kg"};
    if (group === "고기소" || group === "젖소") return {limit: 100, unit: "Bq/kg"};
    if (group === "수산") return {limit: 40, unit: "Bq/kg"};
    return {limit: 100, unit: "Bq/kg"};
  }
  if (analyte === "방사능-I131") return {limit: 300, unit: "Bq/kg"};
  
  return null;
}

export default function FeedCalculator() {
  const [feedClass, setFeedClass] = useState(FEED_CLASS.COMPOUND);
  const [species, setSpecies] = useState("양돈");
  const [profile, setProfile] = useState("양돈-포유자돈");
  const [hideNoLimit, setHideNoLimit] = useState(true);
  const [measurements, setMeasurements] = useState({});
  const [shareSuccess, setShareSuccess] = useState(false);

  useEffect(() => {
    const params = new URLSearchParams(window.location.search);
    const urlFeedClass = params.get('fc');
    const urlSpecies = params.get('sp');
    const urlProfile = params.get('pr');
    const urlHide = params.get('h');
    const urlData = params.get('d');

    if (urlFeedClass || urlSpecies || urlProfile) {
      if (urlFeedClass) setFeedClass(urlFeedClass);
      if (urlSpecies) setSpecies(urlSpecies);
      if (urlProfile) setProfile(urlProfile);
      if (urlHide) setHideNoLimit(urlHide === '1');
      if (urlData) {
        try {
          setMeasurements(JSON.parse(decodeURIComponent(urlData)));
        } catch (e) {
          console.error('Failed to parse URL data');
        }
      }
    } else {
      const saved = localStorage.getItem('feedCalculatorState');
      if (saved) {
        try {
          const state = JSON.parse(saved);
          setFeedClass(state.feedClass || FEED_CLASS.COMPOUND);
          setSpecies(state.species || "양돈");
          setProfile(state.profile || "양돈-포유자돈");
          setHideNoLimit(state.hideNoLimit !== false);
          setMeasurements(state.measurements || {});
        } catch (e) {
          console.error('Failed to load saved state');
        }
      }
    }
  }, []);

  useEffect(() => {
    const state = {feedClass, species, profile, hideNoLimit, measurements};
    localStorage.setItem('feedCalculatorState', JSON.stringify(state));
  }, [feedClass, species, profile, hideNoLimit, measurements]);

  const getProfiles = () => {
    if (feedClass === FEED_CLASS.COMPOUND) {
      return Object.keys(PROFILES_COMPOUND)
        .filter(k => k.startsWith(species + "-"))
        .map(full => ({full, stage: full.split('-').slice(1).join('-')}))
        .sort((a, b) => a.stage.localeCompare(b.stage, "ko"));
    }
    return [{full: `${species}-공통`, stage: "공통"}];
  };

  useEffect(() => {
    const profiles = getProfiles();
    if (profiles.length > 0 && !profiles.find(p => p.full === profile)) {
      setProfile(profiles[0].full);
    }
  }, [species, feedClass]);

  const handleMeasurementChange = (analyte, value) => {
    setMeasurements(prev => ({
      ...prev,
      [analyte]: value
    }));
  };

  const handleShare = () => {
    const params = new URLSearchParams();
    params.set('fc', feedClass);
    params.set('sp', species);
    params.set('pr', profile);
    params.set('h', hideNoLimit ? '1' : '0');
    
    const dataToShare = Object.entries(measurements)
      .filter(([_, v]) => v !== '' && v !== null && v !== undefined)
      .reduce((acc, [k, v]) => ({...acc, [k]: v}), {});
    
    if (Object.keys(dataToShare).length > 0) {
      params.set('d', encodeURIComponent(JSON.stringify(dataToShare)));
    }
    
    const url = `${window.location.origin}${window.location.pathname}?${params.toString()}`;
    
    navigator.clipboard.writeText(url).then(() => {
      setShareSuccess(true);
      setTimeout(() => setShareSuccess(false), 2000);
    });
  };

  const calculateRow = (analyte) => {
    const group = profile.split('-')[0] || "";
    const isHazard = HAZARD_ANALYTES.includes(analyte);
    
    let lim = null;
    if (feedClass === FEED_CLASS.COMPOUND) {
      const profileRules = PROFILES_COMPOUND[profile] || {};
      lim = profileRules[analyte];
      if (!lim) {
        const gdef = (GROUP_DEFAULTS_COMPOUND[group] || {})[analyte];
        if (gdef) lim = gdef;
      }
      if (!lim) {
        const hz = limitFromHazard16(analyte, group, profile);
        if (hz) lim = hz;
      }
    } else {
      const hz = limitFromHazard16(analyte, group, profile);
      if (hz) lim = hz;
    }

    if (!lim || typeof lim.limit !== "number") {
      return {hasLimit: false};
    }

    const feedClassForTol = isHazard ? FEED_CLASS.HARMFUL : feedClass;
    const measured = parseFloat(measurements[analyte]);
    const hasMeasurement = !isNaN(measured);

    const allowed = computeAllowed({
      reg: lim.limit,
      analyte,
      feedClass: feedClassForTol
    });

    let result = {
      hasLimit: true,
      limit: `≤ ${lim.limit} ${lim.unit || ""}`.trim(),
      tolerance: "-",
      allowedBoundary: `≤ ${lim.limit.toFixed(2)}`,
      pass: null
    };

    if (allowed && allowed.status === "OK") {
      if (allowed.rule.type === "relative") {
        result.tolerance = `±${(allowed.rule.pct || 0).toFixed(2)}%`;
      } else if (allowed.rule.type === "absolute") {
        result.tolerance = `±${(allowed.rule.abs || 0).toFixed(2)}`;
      }
      result.allowedBoundary = `≤ ${allowed.allowedMax.toFixed(2)}`;

      if (hasMeasurement) {
        const evalResult = evaluate({
          measured,
          reg: lim.limit,
          analyte,
          feedClass: feedClassForTol
        });
        result.pass = evalResult.pass;
      }
    } else {
      if (hasMeasurement) {
        result.pass = measured <= lim.limit;
      }
    }

    return result;
  };

  const allAnalytes = [...CORE_ANALYTES, ...HAZARD_ANALYTES];
  const visibleAnalytes = hideNoLimit 
    ? allAnalytes.filter(a => calculateRow(a).hasLimit)
    : allAnalytes;

  const feedClassOptions = [
    {key: FEED_CLASS.COMPOUND, label: "배합사료"},
    {key: FEED_CLASS.SINGLE_PLANT_OR_ANIMAL, label: "단미사료(식물/동물)"},
    {key: FEED_CLASS.SINGLE_MINERAL, label: "단미사료(광물)"},
    {key: FEED_CLASS.SUPPLEMENT, label: "보조사료"},
  ];

  return (
    <div className="min-h-screen bg-gray-50 text-gray-900 p-4">
      <div className="max-w-7xl mx-auto">
        <div className="bg-white rounded-2xl p-6 shadow-lg border border-gray-200">
          <div className="flex justify-between items-center mb-6">
            <h1 className="text-2xl font-bold text-gray-900">허용오차 및 제한기준 계산기</h1>
            <button
              onClick={handleShare}
              className="flex items-center gap-2 px-4 py-2 bg-blue-600 hover:bg-blue-700 text-white rounded-lg transition-colors"
            >
              {shareSuccess ? <Check className="w-4 h-4" /> : <Share2 className="w-4 h-4" />}
              {shareSuccess ? '복사됨!' : '링크 공유'}
            </button>
          </div>

          <div className="mb-4">
            <label className="text-sm text-gray-600 mb-2 block font-medium">사료 유형</label>
            <div className="grid grid-cols-2 md:grid-cols-4 gap-2">
              {feedClassOptions.map(opt => (
                <button
                  key={opt.key}
                  onClick={() => setFeedClass(opt.key)}
                  className={`px-3 py-2 rounded-lg text-sm transition-all ${
                    feedClass === opt.key
                      ? 'bg-blue-600 text-white border border-blue-700 shadow-sm'
                      : 'bg-gray-100 text-gray-700 border border-gray-300 hover:bg-gray-200'
                  }`}
                >
                  {opt.label}
                </button>
              ))}
            </div>
          </div>

          <div className="mb-4">
            <label className="flex items-center gap-2 text-sm text-gray-600">
              <input
                type="checkbox"
                checked={hideNoLimit}
                onChange={(e) => setHideNoLimit(e.target.checked)}
                className="rounded"
              />
              제한기준 있는 항목만 보기
            </label>
          </div>

          <div className="mb-4">
            <label className="text-sm text-gray-600 mb-2 block font-medium">축종</label>
            <div className="grid grid-cols-5 gap-2">
              {SPECIES.map(sp => (
                <button
                  key={sp}
                  onClick={() => setSpecies(sp)}
                  className={`px-3 py-2 rounded-lg text-sm transition-all ${
                    species === sp
                      ? 'bg-blue-600 text-white border border-blue-700 shadow-sm'
                      : 'bg-gray-100 text-gray-700 border border-gray-300 hover:bg-gray-200'
                  }`}
                >
                  {sp}
                </button>
              ))}
            </div>
          </div>

          <div className="mb-6">
            <label className="text-sm text-gray-600 mb-2 block font-medium">단계</label>
            <div className="grid grid-cols-3 md:grid-cols-5 gap-2">
              {getProfiles().map(p => (
                <button
                  key={p.full}
                  onClick={() => setProfile(p.full)}
                  className={`px-3 py-2 rounded-lg text-sm transition-all ${
                    profile === p.full
                      ? 'bg-blue-600 text-white border border-blue-700 shadow-sm'
                      : 'bg-gray-100 text-gray-700 border border-gray-300 hover:bg-gray-200'
                  }`}
                >
                  {p.stage}
                </button>
              ))}
            </div>
          </div>

          <div className="overflow-x-auto">
            <table className="w-full text-sm border-collapse" style={{tableLayout: 'fixed'}}>
              <colgroup>
                <col style={{width: '16%'}} />
                <col style={{width: '16%'}} />
                <col style={{width: '16%'}} />
                <col style={{width: '18%'}} />
                <col style={{width: '16%'}} />
                <col style={{width: '18%'}} />
              </colgroup>
              <thead>
                <tr className="border-b-2 border-gray-300 bg-gray-50">
                  <th className="text-left p-3 font-semibold text-gray-700">항목</th>
                  <th className="text-left p-3 font-semibold text-gray-700">분석값</th>
                  <th className="text-left p-3 font-semibold text-gray-700">허용오차</th>
                  <th className="text-left p-3 font-semibold text-gray-700">허용경계</th>
                  <th className="text-left p-3 font-semibold text-gray-700">판정</th>
                  <th className="text-left p-3 font-semibold text-gray-700">제한기준</th>
                </tr>
              </thead>
              <tbody>
                {visibleAnalytes.map(analyte => {
                  const row = calculateRow(analyte);
                  return (
                    <tr key={analyte} className="border-b border-gray-200 hover:bg-gray-50">
                      <td className="p-3 text-gray-900">{analyte}</td>
                      <td className="p-3">
                        <input
                          type="number"
                          step="any"
                          placeholder="분석값"
                          value={measurements[analyte] || ''}
                          onChange={(e) => handleMeasurementChange(analyte, e.target.value)}
                          className="w-full px-2 py-1 bg-white border border-gray-300 rounded text-gray-900 text-sm focus:outline-none focus:ring-2 focus:ring-blue-500"
                        />
                      </td>
                      <td className="p-3 text-gray-600">{row.tolerance || '-'}</td>
                      <td className="p-3 text-gray-600">{row.allowedBoundary || '-'}</td>
                      <td className="p-3">
                        {row.pass === null ? (
                          <span className="text-gray-400">-</span>
                        ) : row.pass ? (
                          <span className="inline-flex items-center gap-1 px-2 py-1 bg-green-100 text-green-700 border border-green-300 rounded-full text-xs font-medium">
                            <Check className="w-3 h-3" /> pass
                          </span>
                        ) : (
                          <span className="inline-flex items-center gap-1 px-2 py-1 bg-red-100 text-red-700 border border-red-300 rounded-full text-xs font-medium">
                            <X className="w-3 h-3" /> fail
                          </span>
                        )}
                      </td>
                      <td className="p-3 text-gray-600">{row.limit || '-'}</td>
                    </tr>
                  );
                })}
              </tbody>
            </table>
          </div>

          <div className="mt-4 text-xs text-gray-500">
            레퍼런스: 「사료검사기준」 [별표 4], 「사료 등의 기준 및 규격」 [별표 21], 「사료 등의 기준 및 규격」 [별표 16]
          </div>
        </div>
      </div>
    </div>
  );
}
