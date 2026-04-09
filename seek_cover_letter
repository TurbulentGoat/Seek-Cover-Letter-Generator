// ==UserScript==
// @name         Seek Cover Letter Generator (Customisable)
// @namespace    http://tampermonkey.net/
// @version      3.0
// @description  Cover letter generator for seek.com.au — templates and personal details are fully editable and saved to localStorage.
// @author       TG
// @match        *://www.seek.com.au/*
// @grant        GM_addStyle
// ==/UserScript==

(function() {
    'use strict';

    // ── Element Blocker ──
    const BLOCK_SELECTOR = '._17igs2j0._36523f85._36523f5p._36523fj._36523fc.fy21pi10.fy21pi13._36523f33._36523f36';
    GM_addStyle(`${BLOCK_SELECTOR} { display: none !important; }`);
    const blockObserver = new MutationObserver(() => {
        document.querySelectorAll(BLOCK_SELECTOR).forEach(el => {
            el.style.setProperty('display', 'none', 'important');
        });
    });
    if (document.body) {
        blockObserver.observe(document.body, { childList: true, subtree: true });
    } else {
        document.addEventListener('DOMContentLoaded', () => {
            blockObserver.observe(document.body, { childList: true, subtree: true });
        });
    }

    // ── Storage helpers ──
    const STORAGE_KEY = 'seek-cl-config';

    const DEFAULT_CONFIG = {
        name: 'Your Name',
        defaultSkills: 'problem-solving, organisation, safety awareness',
        signOff: {
            professional: 'Sincerely',
            friendly: 'Kind regards',
            concise: 'Regards'
        },
        templates: {
            professional: `Dear Hiring Manager,

I am writing to express my interest in the {{jobTitle}} position at {{companyName}}. I believe my background and skills make me a strong candidate for this opportunity.

{{aboutMe}}

{{skillLine}}

I work effectively both independently and as part of a team. My time at university has reinforced the value of collaboration and trusting the strengths of others, and I have developed a well-rounded approach to teamwork as a result.{{whyLine}}

Thank you for considering my application. I would welcome the opportunity to discuss how my skills and enthusiasm can contribute to the continued success of {{companyName}}.

{{signOff}},
{{name}}`,

            friendly: `Dear Hiring Manager,

I am excited to apply for the {{jobTitle}} role at {{companyName}}, it sounds like a great fit for where I am heading in my career.

{{aboutMe}}

{{skillLine}}

I enjoy working independently but have also come to genuinely value teamwork. University has taught me that trusting others' strengths leads to better outcomes for everyone.{{whyLine}}

I would love the chance to chat about how I can contribute to the team at {{companyName}}. Thanks so much for your time!

{{signOff}},
{{name}}`,

            concise: `Dear Hiring Manager,

I wish to apply for the {{jobTitle}} position at {{companyName}}.

{{aboutMe}} {{skillLine}}{{whyLine}}

I would welcome the opportunity to discuss my suitability for this role. Thank you for your consideration.

{{signOff}},
{{name}}`
        },
        university: '',
        course: '',
        aboutMe: ''
    };

    function loadConfig() {
        try {
            const stored = localStorage.getItem(STORAGE_KEY);
            if (stored) {
                const parsed = JSON.parse(stored);
                // Merge with defaults so new keys are always present
                return {
                    ...DEFAULT_CONFIG,
                    ...parsed,
                    signOff: { ...DEFAULT_CONFIG.signOff, ...(parsed.signOff || {}) },
                    templates: { ...DEFAULT_CONFIG.templates, ...(parsed.templates || {}) }
                };
            }
        } catch { /* ignore corrupt data */ }
        return { ...DEFAULT_CONFIG };
    }

    function saveConfig(config) {
        localStorage.setItem(STORAGE_KEY, JSON.stringify(config));
    }

    let config = loadConfig();

    // ── Styles ──
    GM_addStyle(`
        #cl-toggle {
            position: fixed; top: 200px; right: 0; z-index: 10000;
            background: #e60278; color: #fff; border: none;
            border-radius: 8px 0 0 8px; padding: 12px 8px; cursor: pointer;
            font-family: SeekSans, "SeekSans Fallback", Arial, sans-serif;
            font-size: 13px; font-weight: 600;
            writing-mode: vertical-rl; text-orientation: mixed; letter-spacing: 1px;
            box-shadow: -2px 2px 8px rgba(0,0,0,0.15); transition: background 0.2s;
        }
        #cl-toggle:hover { background: #c4025f; }

        #cl-panel {
            position: fixed; top: 80px; right: -380px; width: 360px;
            max-height: calc(100vh - 100px);
            z-index: 10001; background: #fff;
            border-radius: 12px 0 0 12px;
            box-shadow: -4px 0 24px rgba(0,0,0,0.12);
            font-family: SeekSans, "SeekSans Fallback", Arial, sans-serif;
            transition: right 0.3s ease;
            display: flex; flex-direction: column;
        }
        #cl-panel.open { right: 0; }

        #cl-panel-header {
            background: #e60278; color: #fff; padding: 16px 20px;
            font-size: 16px; font-weight: 700;
            display: flex; align-items: center; justify-content: space-between;
            flex-shrink: 0;
        }
        #cl-panel-header button {
            background: none; border: none; color: #fff;
            font-size: 20px; cursor: pointer; line-height: 1;
        }

        #cl-panel-tabs {
            display: flex; border-bottom: 1px solid #eee; flex-shrink: 0;
        }
        .cl-tab {
            flex: 1; padding: 10px; text-align: center;
            font-size: 13px; font-weight: 600; color: #999;
            cursor: pointer; border: none; background: none;
            border-bottom: 2px solid transparent;
            font-family: inherit; transition: color 0.2s, border-color 0.2s;
        }
        .cl-tab.active { color: #e60278; border-bottom-color: #e60278; }
        .cl-tab:hover { color: #333; }

        #cl-panel-body {
            padding: 20px; display: flex; flex-direction: column; gap: 14px;
            overflow-y: auto; flex: 1;
        }
        #cl-panel-body label {
            font-size: 13px; font-weight: 600; color: #333;
            margin-bottom: 2px; display: block;
        }
        #cl-panel-body select,
        #cl-panel-body input,
        #cl-panel-body textarea {
            width: 100%; padding: 10px 12px;
            border: 1.5px solid #ddd; border-radius: 8px;
            font-family: inherit; font-size: 14px;
            box-sizing: border-box; transition: border-color 0.2s;
        }
        #cl-panel-body select:focus,
        #cl-panel-body input:focus,
        #cl-panel-body textarea:focus {
            outline: none; border-color: #e60278;
        }
        #cl-panel-body textarea { resize: vertical; min-height: 60px; }

        .cl-btn {
            border: none; border-radius: 8px; padding: 12px;
            font-size: 14px; font-weight: 700; cursor: pointer;
            font-family: inherit; transition: background 0.2s; width: 100%;
        }
        .cl-btn-primary { background: #e60278; color: #fff; }
        .cl-btn-primary:hover { background: #c4025f; }
        .cl-btn-secondary { background: #f0f0f0; color: #333; }
        .cl-btn-secondary:hover { background: #e0e0e0; }
        .cl-btn-danger { background: #dc3545; color: #fff; }
        .cl-btn-danger:hover { background: #b52d3a; }
        .cl-btn-success { background: #28a745; color: #fff; }
        .cl-btn-success:hover { background: #1e7e34; }

        .cl-field-group { display: flex; flex-direction: column; gap: 4px; }
        .cl-btn-row { display: flex; gap: 8px; }
        .cl-btn-row .cl-btn { flex: 1; }

        .cl-help {
            font-size: 11px; color: #999; margin-top: 2px; line-height: 1.4;
        }

        .cl-section-title {
            font-size: 14px; font-weight: 700; color: #333;
            padding-bottom: 6px; border-bottom: 1px solid #eee;
            margin-bottom: 0;
        }

        #cl-modal-overlay {
            position: fixed; inset: 0; z-index: 10002;
            background: rgba(0,0,0,0.45); backdrop-filter: blur(4px);
            display: flex; align-items: center; justify-content: center;
            opacity: 0; pointer-events: none; transition: opacity 0.25s;
        }
        #cl-modal-overlay.visible { opacity: 1; pointer-events: auto; }

        #cl-modal {
            background: #fff; border-radius: 14px;
            width: 680px; max-width: 92vw; max-height: 85vh;
            display: flex; flex-direction: column;
            box-shadow: 0 12px 40px rgba(0,0,0,0.2);
            font-family: SeekSans, "SeekSans Fallback", Arial, sans-serif;
            transform: translateY(20px); transition: transform 0.25s;
        }
        #cl-modal-overlay.visible #cl-modal { transform: translateY(0); }

        #cl-modal-header {
            display: flex; align-items: center; justify-content: space-between;
            padding: 18px 24px; border-bottom: 1px solid #eee;
        }
        #cl-modal-header h2 { margin: 0; font-size: 18px; font-weight: 700; color: #333; }
        #cl-modal-close {
            background: none; border: none; font-size: 22px;
            cursor: pointer; color: #999; transition: color 0.2s;
        }
        #cl-modal-close:hover { color: #333; }

        #cl-modal-body { padding: 20px 24px; flex: 1; overflow-y: auto; }
        #cl-modal-body textarea {
            width: 100%; min-height: 340px;
            border: 1.5px solid #ddd; border-radius: 8px;
            padding: 16px; font-family: inherit; font-size: 14px;
            line-height: 1.6; resize: vertical; box-sizing: border-box;
        }
        #cl-modal-body textarea:focus { outline: none; border-color: #e60278; }

        #cl-modal-footer {
            padding: 14px 24px; border-top: 1px solid #eee;
            display: flex; justify-content: flex-end; gap: 10px;
        }
        .cl-modal-btn {
            padding: 10px 22px; border-radius: 8px; font-size: 14px;
            font-weight: 600; cursor: pointer; border: none;
            font-family: inherit; transition: background 0.2s;
        }
        .cl-modal-btn.primary { background: #e60278; color: #fff; }
        .cl-modal-btn.primary:hover { background: #c4025f; }
        .cl-modal-btn.secondary { background: #f0f0f0; color: #333; }
        .cl-modal-btn.secondary:hover { background: #e0e0e0; }

        #cl-toast {
            position: fixed; bottom: 30px; left: 50%;
            transform: translateX(-50%) translateY(20px);
            background: #1a1a1a; color: #fff; padding: 12px 28px;
            border-radius: 8px;
            font-family: SeekSans, "SeekSans Fallback", Arial, sans-serif;
            font-size: 14px; font-weight: 600;
            z-index: 10003; opacity: 0; pointer-events: none;
            transition: opacity 0.3s, transform 0.3s;
        }
        #cl-toast.show { opacity: 1; transform: translateX(-50%) translateY(0); }
    `);

    // ── Template rendering ──
    function renderTemplate(templateStr, vars) {
        return templateStr.replace(/\{\{(\w+)\}\}/g, (_, key) => {
            return vars[key] !== undefined ? vars[key] : '';
        });
    }

    function buildAboutMe() {
        // If user wrote a custom aboutMe, use it directly
        if (config.aboutMe.trim()) return config.aboutMe.trim();
        // Otherwise auto-generate from structured fields
        const hasUni = config.university.trim();
        const hasCourse = config.course.trim();
        if (hasCourse && hasUni) {
            return `I am currently studying ${config.course.trim()} at ${config.university.trim()}.`;
        } else if (hasUni) {
            return `I am currently studying at ${config.university.trim()}.`;
        } else if (hasCourse) {
            return `I am currently studying ${config.course.trim()}.`;
        }
        return '';
    }

    function generateLetter(tone, jobTitle, companyName, skills, whyRole) {
        const skillLine = skills.trim()
            ? `My key strengths include ${skills.trim()}, which I am eager to apply in this role.`
            : '';
        const whyLine = whyRole.trim() ? `\n\n${whyRole.trim()}` : '';
        const template = config.templates[tone] || config.templates.professional;
        const signOff = config.signOff[tone] || config.signOff.professional;

        return renderTemplate(template, {
            jobTitle,
            companyName,
            name: config.name,
            aboutMe: buildAboutMe(),
            skillLine,
            whyLine,
            signOff
        });
    }

    // ── Toast ──
    const toast = document.createElement('div');
    toast.id = 'cl-toast';
    document.body.appendChild(toast);

    function showToast(msg) {
        toast.textContent = msg;
        toast.classList.add('show');
        setTimeout(() => toast.classList.remove('show'), 2000);
    }

    // ── Toggle Tab ──
    const toggle = document.createElement('button');
    toggle.id = 'cl-toggle';
    toggle.textContent = 'Cover Letter';
    document.body.appendChild(toggle);

    // ── Panel Tab Views ──
    function buildGenerateView() {
        return `
            <div class="cl-field-group">
                <label for="cl-tone">Tone</label>
                <select id="cl-tone">
                    <option value="professional" selected>Professional</option>
                    <option value="friendly">Friendly</option>
                    <option value="concise">Concise</option>
                </select>
            </div>
            <div class="cl-field-group">
                <label for="cl-skills">Key skills to highlight</label>
                <input type="text" id="cl-skills" value="${escAttr(config.defaultSkills)}" placeholder="e.g. communication, leadership">
            </div>
            <div class="cl-field-group">
                <label for="cl-why">Why this role? (optional)</label>
                <textarea id="cl-why" placeholder="What specifically draws you to this position or company?"></textarea>
            </div>
            <button class="cl-btn cl-btn-primary" id="cl-generate-btn">Generate Cover Letter</button>
        `;
    }

    function buildSettingsView() {
        return `
            <div class="cl-section-title">Personal Details</div>
            <div class="cl-field-group">
                <label for="cl-cfg-name">Full Name</label>
                <input type="text" id="cl-cfg-name" value="${escAttr(config.name)}">
            </div>
            <div class="cl-field-group">
                <label for="cl-cfg-skills">Default Skills</label>
                <input type="text" id="cl-cfg-skills" value="${escAttr(config.defaultSkills)}">
            </div>

            <div class="cl-section-title">Education (optional)</div>
            <div class="cl-field-group">
                <label for="cl-cfg-uni">University / Institution</label>
                <input type="text" id="cl-cfg-uni" value="${escAttr(config.university)}" placeholder="e.g. Curtin University">
            </div>
            <div class="cl-field-group">
                <label for="cl-cfg-course">Course / Degree</label>
                <input type="text" id="cl-cfg-course" value="${escAttr(config.course)}" placeholder="e.g. Bachelor of Science">
                <div class="cl-help">Leave both blank if not studying.</div>
            </div>

            <div class="cl-section-title">About Me</div>
            <div class="cl-field-group">
                <label for="cl-cfg-about">Custom About Me (optional)</label>
                <textarea id="cl-cfg-about" style="min-height:100px" placeholder="Leave blank to auto-generate from your education details above.">${escHtml(config.aboutMe)}</textarea>
                <div class="cl-help">If filled, this overrides the auto-generated text. Used as {{aboutMe}} in templates.</div>
            </div>

            <div class="cl-section-title">Sign-offs</div>
            <div class="cl-field-group">
                <label for="cl-cfg-signoff-pro">Professional</label>
                <input type="text" id="cl-cfg-signoff-pro" value="${escAttr(config.signOff.professional)}">
            </div>
            <div class="cl-field-group">
                <label for="cl-cfg-signoff-fri">Friendly</label>
                <input type="text" id="cl-cfg-signoff-fri" value="${escAttr(config.signOff.friendly)}">
            </div>
            <div class="cl-field-group">
                <label for="cl-cfg-signoff-con">Concise</label>
                <input type="text" id="cl-cfg-signoff-con" value="${escAttr(config.signOff.concise)}">
            </div>

            <button class="cl-btn cl-btn-success" id="cl-save-settings">Save Settings</button>
        `;
    }

    function buildTemplatesView() {
        return `
            <div class="cl-help" style="margin-bottom:8px">
                Use placeholders: {{jobTitle}}, {{companyName}}, {{name}}, {{aboutMe}}, {{skillLine}}, {{whyLine}}, {{signOff}}
            </div>
            <div class="cl-field-group">
                <label for="cl-tpl-pro">Professional Template</label>
                <textarea id="cl-tpl-pro" style="min-height:160px">${escHtml(config.templates.professional)}</textarea>
            </div>
            <div class="cl-field-group">
                <label for="cl-tpl-fri">Friendly Template</label>
                <textarea id="cl-tpl-fri" style="min-height:160px">${escHtml(config.templates.friendly)}</textarea>
            </div>
            <div class="cl-field-group">
                <label for="cl-tpl-con">Concise Template</label>
                <textarea id="cl-tpl-con" style="min-height:160px">${escHtml(config.templates.concise)}</textarea>
            </div>
            <div class="cl-btn-row">
                <button class="cl-btn cl-btn-success" id="cl-save-templates">Save Templates</button>
                <button class="cl-btn cl-btn-danger" id="cl-reset-templates">Reset Defaults</button>
            </div>
        `;
    }

    // ── Escape helpers ──
    function escHtml(s) {
        return s.replace(/&/g,'&amp;').replace(/</g,'&lt;').replace(/>/g,'&gt;').replace(/"/g,'&quot;');
    }
    function escAttr(s) {
        return s.replace(/&/g,'&amp;').replace(/"/g,'&quot;').replace(/</g,'&lt;').replace(/>/g,'&gt;');
    }

    // ── Side Panel ──
    const panel = document.createElement('div');
    panel.id = 'cl-panel';
    panel.innerHTML = `
        <div id="cl-panel-header">
            <span>Cover Letter</span>
            <button id="cl-panel-close">&times;</button>
        </div>
        <div id="cl-panel-tabs">
            <button class="cl-tab active" data-tab="generate">Generate</button>
            <button class="cl-tab" data-tab="settings">Settings</button>
            <button class="cl-tab" data-tab="templates">Templates</button>
        </div>
        <div id="cl-panel-body"></div>
    `;
    document.body.appendChild(panel);

    const panelBody = panel.querySelector('#cl-panel-body');
    let activeTab = 'generate';

    function switchTab(tab) {
        activeTab = tab;
        panel.querySelectorAll('.cl-tab').forEach(t => {
            t.classList.toggle('active', t.dataset.tab === tab);
        });
        if (tab === 'generate') {
            panelBody.innerHTML = buildGenerateView();
            bindGenerateEvents();
        } else if (tab === 'settings') {
            panelBody.innerHTML = buildSettingsView();
            bindSettingsEvents();
        } else if (tab === 'templates') {
            panelBody.innerHTML = buildTemplatesView();
            bindTemplatesEvents();
        }
    }

    function bindGenerateEvents() {
        panelBody.querySelector('#cl-generate-btn').addEventListener('click', () => {
            let jobTitle = document.querySelector('[data-automation="job-detail-title"]')?.innerText || 'Job Title';
            const companyName = document.querySelector('[data-automation="advertiser-name"]')?.innerText || 'Company Name';
            jobTitle = jobTitle.split('-')[0].trim();

            const tone = panelBody.querySelector('#cl-tone').value;
            const skills = panelBody.querySelector('#cl-skills').value;
            const whyRole = panelBody.querySelector('#cl-why').value;

            const letter = generateLetter(tone, jobTitle, companyName, skills, whyRole);
            panel.classList.remove('open');
            openModal(letter);
        });
    }

    function bindSettingsEvents() {
        panelBody.querySelector('#cl-save-settings').addEventListener('click', () => {
            config.name = panelBody.querySelector('#cl-cfg-name').value.trim() || config.name;
            config.defaultSkills = panelBody.querySelector('#cl-cfg-skills').value.trim();
            config.university = panelBody.querySelector('#cl-cfg-uni').value.trim();
            config.course = panelBody.querySelector('#cl-cfg-course').value.trim();
            config.aboutMe = panelBody.querySelector('#cl-cfg-about').value.trim();
            config.signOff.professional = panelBody.querySelector('#cl-cfg-signoff-pro').value.trim();
            config.signOff.friendly = panelBody.querySelector('#cl-cfg-signoff-fri').value.trim();
            config.signOff.concise = panelBody.querySelector('#cl-cfg-signoff-con').value.trim();
            saveConfig(config);
            showToast('Settings saved!');
        });
    }

    function bindTemplatesEvents() {
        panelBody.querySelector('#cl-save-templates').addEventListener('click', () => {
            config.templates.professional = panelBody.querySelector('#cl-tpl-pro').value;
            config.templates.friendly = panelBody.querySelector('#cl-tpl-fri').value;
            config.templates.concise = panelBody.querySelector('#cl-tpl-con').value;
            saveConfig(config);
            showToast('Templates saved!');
        });
        panelBody.querySelector('#cl-reset-templates').addEventListener('click', () => {
            config.templates = { ...DEFAULT_CONFIG.templates };
            config.signOff = { ...DEFAULT_CONFIG.signOff };
            saveConfig(config);
            switchTab('templates');
            showToast('Templates reset to defaults!');
        });
    }

    // Init panel
    switchTab('generate');

    toggle.addEventListener('click', () => panel.classList.toggle('open'));
    panel.querySelector('#cl-panel-close').addEventListener('click', () => panel.classList.remove('open'));
    panel.querySelector('#cl-panel-tabs').addEventListener('click', (e) => {
        if (e.target.classList.contains('cl-tab')) {
            switchTab(e.target.dataset.tab);
        }
    });

    // ── Modal Overlay ──
    const overlay = document.createElement('div');
    overlay.id = 'cl-modal-overlay';
    overlay.innerHTML = `
        <div id="cl-modal">
            <div id="cl-modal-header">
                <h2>Your Cover Letter</h2>
                <button id="cl-modal-close">&times;</button>
            </div>
            <div id="cl-modal-body">
                <textarea id="cl-modal-text"></textarea>
            </div>
            <div id="cl-modal-footer">
                <button class="cl-modal-btn secondary" id="cl-modal-cancel">Close</button>
                <button class="cl-modal-btn primary" id="cl-modal-copy">Copy &amp; Close</button>
            </div>
        </div>
    `;
    document.body.appendChild(overlay);

    function openModal(text) {
        overlay.querySelector('#cl-modal-text').value = text;
        overlay.classList.add('visible');
    }
    function closeModal() {
        overlay.classList.remove('visible');
    }

    overlay.addEventListener('click', (e) => { if (e.target === overlay) closeModal(); });
    overlay.querySelector('#cl-modal-close').addEventListener('click', closeModal);
    overlay.querySelector('#cl-modal-cancel').addEventListener('click', closeModal);
    overlay.querySelector('#cl-modal-copy').addEventListener('click', async () => {
        const text = overlay.querySelector('#cl-modal-text').value;
        try {
            await navigator.clipboard.writeText(text);
            showToast('Copied to clipboard!');
            closeModal();
        } catch {
            const ta = document.createElement('textarea');
            ta.value = text;
            ta.style.position = 'fixed';
            ta.style.opacity = '0';
            document.body.appendChild(ta);
            ta.select();
            document.execCommand('copy');
            document.body.removeChild(ta);
            showToast('Copied to clipboard!');
            closeModal();
        }
    });

})();
