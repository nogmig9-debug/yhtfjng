<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Нейросети и учитель: аналитический обзор</title>
    <style>
        /* Стили без изменений */
        body {
            font-family: 'Segoe UI', Roboto, 'Helvetica Neue', sans-serif;
            background: #0f172a;
            color: #e2e8f0;
            line-height: 1.6;
            overflow: hidden;
            height: 100vh;
            margin: 0;
            padding: 0;
        }

        .slider {
            height: 100vh;
            overflow-y: scroll;
            scroll-snap-type: y mandatory;
            scroll-behavior: smooth;
            -webkit-overflow-scrolling: touch;
        }

        .slide {
            min-height: 100vh;
            scroll-snap-align: start;
            display: flex;
            flex-direction: column;
            justify-content: center;
            align-items: center;
            padding: 3rem 2rem;
            position: relative;
            background: #0f172a;
            border-bottom: 1px solid #2d3a4f;
        }

        .slide:nth-child(odd) {
            background: #0a0f1a;
        }

        .container {
            max-width: 1000px;
            width: 100%;
            margin: 0 auto;
        }

        h1 {
            font-size: clamp(2.2rem, 8vw, 4rem);
            font-weight: 800;
            letter-spacing: -0.02em;
            margin-bottom: 1.5rem;
            color: #ffffff;
            line-height: 1.2;
        }

        h2 {
            font-size: clamp(1.8rem, 6vw, 2.8rem);
            font-weight: 700;
            margin-bottom: 2rem;
            color: #f1f5f9;
            position: relative;
            display: inline-block;
        }

        h2::after {
            content: '';
            position: absolute;
            bottom: -10px;
            left: 0;
            width: 80px;
            height: 4px;
            background: #4b5565;
            border-radius: 2px;
        }

        p {
            font-size: 1.2rem;
            margin-bottom: 1.2rem;
            color: #d1d5db;
            text-align: justify;
        }

        .highlight {
            font-weight: 600;
            color: inherit;
        }

        .list {
            list-style: none;
            margin: 1.5rem 0;
        }
        .list li {
            margin-bottom: 0.8rem;
            padding-left: 1.5rem;
            position: relative;
            font-size: 1.15rem;
            color: #d1d5db;
        }
        .list li::before {
            content: "•";
            color: #9ca3af;
            font-weight: bold;
            position: absolute;
            left: 0;
        }

        .comparison-grid {
            display: grid;
            grid-template-columns: 1fr 1fr;
            gap: 2rem;
            margin: 2rem 0;
        }
        .comparison-col {
            background: #1e293b;
            border-radius: 24px;
            padding: 1.8rem;
            border: 1px solid #334155;
            box-shadow: 0 25px 50px -12px #000000;
        }
        .comparison-col h3 {
            font-size: 1.8rem;
            margin-bottom: 1rem;
            color: #ffffff;
        }

        .flex-blocks {
            display: flex;
            flex-wrap: wrap;
            gap: 1.5rem;
            margin: 2rem 0;
        }
        .flex-block {
            flex: 1 1 250px;
            background: #1e293b;
            border-radius: 20px;
            padding: 1.5rem;
            border: 1px solid #334155;
        }
        .flex-block h3 {
            color: #ffffff;
            margin-bottom: 1rem;
            font-size: 1.5rem;
        }

        .quote {
            font-style: italic;
            border-left: 4px solid #4b5565;
            padding-left: 1.5rem;
            margin: 1.5rem 0;
            color: #cbd5e1;
            font-size: 1.2rem;
        }

        .nav-arrows {
            position: fixed;
            right: 2rem;
            top: 50%;
            transform: translateY(-50%);
            display: flex;
            flex-direction: column;
            gap: 0.8rem;
            z-index: 100;
        }
        .nav-arrow {
            background: #2d3a4f;
            border: 1px solid #4b5565;
            color: white;
            width: 48px;
            height: 48px;
            border-radius: 50%;
            display: flex;
            align-items: center;
            justify-content: center;
            cursor: pointer;
            backdrop-filter: blur(8px);
            transition: all 0.2s;
            font-size: 1.5rem;
        }
        .nav-arrow:hover {
            background: #3b4a62;
            border-color: #9ca3af;
            transform: scale(1.1);
        }

        .dots {
            position: fixed;
            left: 2rem;
            top: 50%;
            transform: translateY(-50%);
            display: flex;
            flex-direction: column;
            gap: 1rem;
            z-index: 100;
        }
        .dot {
            width: 12px;
            height: 12px;
            border-radius: 50%;
            background: #4b5565;
            border: 1px solid #6b7a8f;
            cursor: pointer;
            transition: 0.2s;
        }
        .dot.active {
            background: #9ca3af;
            border-color: #ffffff;
            transform: scale(1.4);
        }

        .footer-note {
            margin-top: 2rem;
            font-size: 0.9rem;
            color: #6b7a8f;
            text-align: center;
            border-top: 1px solid #2d3a4f;
            padding-top: 1.5rem;
        }

        @media (max-width: 768px) {
            .nav-arrows, .dots { display: none; }
            .slide { padding: 2rem 1rem; }
            p { font-size: 1rem; }
            .comparison-grid { grid-template-columns: 1fr; }
        }

        .content-image {
            width: 100%;
            max-width: 800px;
            border-radius: 16px;
            border: 1px solid #334155;
            margin: 2rem auto;
            display: block;
            box-shadow: 0 10px 20px rgba(0,0,0,0.5);
        }

        .video-link {
            display: inline-block;
            margin: 1.5rem 0 0.5rem 0;
            padding: 0.8rem 2rem;
            background: #1e293b;
            border: 1px solid #4b5565;
            border-radius: 40px;
            color: #e2e8f0;
            text-decoration: none;
            font-size: 1.1rem;
            transition: background 0.2s, border-color 0.2s;
        }
        .video-link:hover {
            background: #2d3a4f;
            border-color: #9ca3af;
        }

        /* Стили для опроса */
        .poll-container {
            background: #1e293b;
            border-radius: 30px;
            padding: 2rem;
            margin: 2rem 0;
            border: 1px solid #334155;
            text-align: center;
        }
        .poll-question {
            font-size: 1.8rem;
            font-weight: 600;
            margin-bottom: 2rem;
            color: #ffffff;
        }
        .poll-buttons {
            display: flex;
            gap: 2rem;
            justify-content: center;
            flex-wrap: wrap;
        }
        .poll-btn {
            flex: 0 1 200px;
            padding: 1rem 2rem;
            font-size: 1.4rem;
            font-weight: 600;
            border: none;
            border-radius: 60px;
            cursor: pointer;
            transition: 0.2s;
            background: #0f172a;
            color: #e2e8f0;
            border: 1px solid #4b5565;
        }
        .poll-btn:hover {
            background: #2d3a4f;
            border-color: #9ca3af;
            transform: scale(1.02);
        }
        .poll-btn:active {
            transform: scale(0.98);
        }
    </style>
</head>
<body>
    <div class="dots" id="dots-container"></div>
    <div class="nav-arrows">
        <div class="nav-arrow" id="arrow-up">↑</div>
        <div class="nav-arrow" id="arrow-down">↓</div>
    </div>

    <div class="slider" id="slider">

        <section class="slide" id="slide1">
            <div class="container">
                <h1>Почему нейросеть не сможет заменить школьного учителя?</h1>
                <p style="font-size: 1.5rem; color: #cbd5e1;">Аналитический взгляд на роль искусственного интеллекта в образовании</p>
                <div style="margin-top: 3rem; display: flex; gap: 2rem; flex-wrap: wrap; justify-content: center;">
                    <div style="background: #1e293b; border-radius: 30px; padding: 0.8rem 2rem; border: 1px solid #334155;">6 ключевых тезисов</div>
                    <div style="background: #1e293b; border-radius: 30px; padding: 0.8rem 2rem; border: 1px solid #334155;">Школьное образование 21 века</div>
                </div>
                <div class="footer-note">Нажмите стрелку вниз или прокручивайте →</div>
            </div>
        </section>

        <section class="slide" id="slide2">
            <div class="container">
                <h2>Что такое нейросети?</h2>
                <p><span class="highlight">Нейронные сети</span> — это математические модели, вдохновленные структурой биологического мозга. Они состоят из множества связанных между собой искусственных нейронов, которые обрабатывают информацию и обучаются на больших массивах данных. Однако важно понимать: <strong>нейросеть не мыслит и не осознаёт</strong> — она лишь выявляет статистические закономерности.</p>
                <p>Современные архитектуры (трансформеры, свёрточные сети) способны классифицировать изображения, генерировать тексты и даже вести диалог. Но вся их «мудрость» — это результат переработки данных, на которых их обучили. Нейросеть не имеет собственного опыта, эмоций, моральных принципов. Она действует в рамках заданного алгоритма и не способна выйти за пределы обучающей выборки.</p>
                <p>Пример: GPT-4 может написать сочинение на тему добра, но не переживает добра, не чувствует ответственности за написанное. Это принципиальное ограничение, которое делает невозможной полноценную замену учителя.</p>
                
                <!-- Вставленное изображение chart (1).png -->
                <img class="content-image" src="chart (1).png" alt="Диаграмма: отношение к нейросетям в образовании">

                <div style="text-align: center;">
                    <a class="video-link" href="https://yandex.ru/video/preview/920911868037396964" target="_blank" rel="noopener noreferrer">
                        Смотреть видео: «Как работают нейросети» (Яндекс.Видео)
                    </a>
                </div>

                <div class="quote">«Нейросети — это мощный инструмент, но они остаются инструментом, лишённым субъектности» — Джеффри Хинтон, один из пионеров ИИ.</div>
                <p><strong>Ключевые особенности:</strong> обучаемость на данных, отсутствие сознания, зависимость от качества данных, неспособность к рефлексии и эмпатии.</p>
            </div>
        </section>

        <section class="slide" id="slide3">
            <div class="container">
                <h2>Настоящее образование — больше, чем информация</h2>
                <p>Образование в школе — это не просто передача знаний (фактов, формул, дат). Это сложный процесс <span class="highlight">формирования личности</span>. Учитель не только объясняет теорему Пифагора, но и учит детей думать, сомневаться, задавать вопросы, взаимодействовать друг с другом, уважать чужое мнение.</p>
                
                <!-- Первое изображение (1.jpg) -->
                <img class="content-image" src="1.jpg" alt="Учитель и ученики за работой">

                <ul class="list">
                    <li><strong>Воспитание:</strong> привитие моральных ценностей, этических норм, патриотизма, уважения к старшим.</li>
                    <li><strong>Социализация:</strong> умение работать в коллективе, решать конфликты, помогать одноклассникам.</li>
                    <li><strong>Эмоциональный интеллект:</strong> распознавание эмоций, эмпатия, поддержка в трудной ситуации.</li>
                    <li><strong>Критическое мышление:</strong> умение анализировать информацию, отличать факты от манипуляций.</li>
                    <li><strong>Индивидуальный подход:</strong> учитель видит, когда ребёнок устал, расстроен или, наоборот, горит идеей, и корректирует взаимодействие.</li>
                </ul>
                <p>Ни одна нейросеть не способна утешить плачущего первоклассника, вдохновить на исследовательский проект или заметить, что у подростка проблемы в семье, и вовремя проявить участие. Образование — это всегда диалог, встреча личностей, а не передача данных.</p>
            </div>
        </section>

        <section class="slide" id="slide4">
            <div class="container">
                <h2>ИИ против учителя: противостояние или дополнение?</h2>
                <div class="comparison-grid">
                    <div class="comparison-col">
                        <h3>Учитель</h3>
                        <ul class="list">
                            <li>Обладает эмпатией, чувствует настроение класса.</li>
                            <li>Может адаптировать объяснение «на лету», используя метафоры и личный опыт.</li>
                            <li>Воспитывает личным примером, передаёт ценности.</li>
                            <li>Способен заметить творческий потенциал и развить его.</li>
                            <li>Несёт моральную ответственность за учеников.</li>
                            <li>Работает с «трудными» детьми, мотивирует, вдохновляет.</li>
                        </ul>
                    </div>
                    <div class="comparison-col">
                        <h3>Нейросеть</h3>
                        <ul class="list">
                            <li>Мгновенно выдает факты, формулы, решает задачи.</li>
                            <li>Может подобрать индивидуальные упражнения на основе анализа ошибок.</li>
                            <li>Доступна 24/7, не устаёт и не раздражается.</li>
                            <li>Обрабатывает огромные объёмы информации.</li>
                            <li>Не имеет чувств, не понимает юмора и иронии.</li>
                            <li>Не способна к творчеству в человеческом смысле.</li>
                        </ul>
                    </div>
                </div>
                <p>Сравнение показывает: у каждой стороны свои сильные стороны. Учитель незаменим в воспитании, мотивации, развитии личности. ИИ эффективен в рутинной передаче информации и тренировке навыков. Поэтому корректнее говорить не о замене, а о распределении ролей.</p>
                
                <!-- Второе изображение (3.jpg) -->
                <img class="content-image" src="3.jpg" alt="Учитель и ученики в классе">
            </div>
        </section>

        <section class="slide" id="slide5">
            <div class="container">
                <h2>Тандем: учитель + ИИ = новая образовательная реальность</h2>
                <p>Вместо того чтобы бояться конкуренции с искусственным интеллектом, педагоги могут использовать его как <span class="highlight">мощного помощника</span>. Освободившись от рутины, учитель получает больше времени для живого общения и творчества.</p>
                <div class="flex-blocks">
                    <div class="flex-block">
                        <h3>Что берёт на себя ИИ</h3>
                        <ul class="list">
                            <li>Проверка тестов и домашних заданий.</li>
                            <li>Генерация упражнений по уровням сложности.</li>
                            <li>Анализ успеваемости и прогнозирование пробелов.</li>
                            <li>Подбор дополнительных материалов (видео, статей).</li>
                        </ul>
                    </div>
                    <div class="flex-block">
                        <h3>Что остаётся учителю</h3>
                        <ul class="list">
                            <li>Живое объяснение сложных концепций.</li>
                            <li>Мотивация и эмоциональная поддержка.</li>
                            <li>Развитие soft skills, дискуссии, проектная работа.</li>
                            <li>Воспитание и личный пример.</li>
                        </ul>
                    </div>
                </div>
                <p>Пример: нейросеть может подготовить для каждого ученика индивидуальные карточки с задачами, а учитель проведёт увлекательную дискуссию о применении этих знаний в жизни. Вместе они создают среду, где технологии усиливают человеческий потенциал, а не заменяют его.</p>
                
                <!-- Третье изображение (photo_2026-03-12_20-39-38.jpg) -->
                <img class="content-image" src="photo_2026-03-12_20-39-38.jpg" alt="AI технологии в образовании">
            </div>
        </section>

        <section class="slide" id="slide6">
            <div class="container">
                <h2>Вывод: незаменимость учителя</h2>
                <p>Проанализировав возможности нейросетей и сущность образования, мы приходим к однозначному выводу: <span class="highlight">полноценная замена школьного учителя искусственным интеллектом невозможна</span>. Школа — это не только знания, это пространство становления человека, где важны эмоции, пример, диалог и доверие.</p>
                <p>Нейросети могут стать отличными ассистентами, взяв на себя техническую сторону обучения, но они никогда не почувствуют, почему ребёнок сегодня грустен, не смогут разделить радость открытия, не научат доброте и честности. Учитель остаётся ключевой фигурой образования, а технологии — лишь инструментом в его руках.</p>
                
                <div style="background: #1e293b; border-radius: 24px; padding: 2rem; margin: 2rem 0; border: 1px solid #334155;">
                    <p style="font-size: 1.2rem; margin:0; color: #e2e8f0;">
                        <strong>Д.А. Иванченко считает что:</strong> Понимание возможностей и ограничений технологий искусственного интеллекта, использование критического подхода и навыков промт-инжиниринга, внедрение этических и правовых норм в деятельность образовательных организаций позволит снизить риски при внедрении нейросетевых технологий и использовать их потенциал более эффективно. Административно-управленческий персонал и педагоги должны быть готовы к осознанному и ответственному использованию новых технологий, соблюдая нормы и правила, регулирующие данную область.
                    </p>
                </div>

                <!-- Опрос на финальном слайде -->
                <div class="poll-container">
                    <div class="poll-question">Сможет ли по вашему мнению нейросеть заменить учителя?</div>
                    <div class="poll-buttons">
                        <button class="poll-btn" id="vote-yes">Да</button>
                        <button class="poll-btn" id="vote-no">Нет</button>
                    </div>
                </div>

                <p style="margin-top: 1rem;">Таким образом, будущее за разумным тандемом, где человек остаётся творцом, а нейросеть — его помощником.</p>
                <div class="footer-note">© Школьный проект о нейросетях, 2026 | Все права защищены</div>
            </div>
        </section>
    </div>

    <script>
        (function() {
            const slider = document.getElementById('slider');
            const slides = document.querySelectorAll('.slide');
            const dotsContainer = document.getElementById('dots-container');
            const arrowUp = document.getElementById('arrow-up');
            const arrowDown = document.getElementById('arrow-down');

            // Генерация точек
            slides.forEach((_, index) => {
                const dot = document.createElement('div');
                dot.classList.add('dot');
                dot.dataset.index = index;
                dot.addEventListener('click', () => {
                    slides[index].scrollIntoView({ behavior: 'smooth' });
                });
                dotsContainer.appendChild(dot);
            });

            const dots = document.querySelectorAll('.dot');

            function updateActiveDot() {
                let activeIndex = 0;
                const scrollTop = slider.scrollTop;
                const slideHeights = [];
                slides.forEach(slide => slideHeights.push(slide.offsetHeight));

                let accumulated = 0;
                for (let i = 0; i < slideHeights.length; i++) {
                    if (scrollTop >= accumulated && scrollTop < accumulated + slideHeights[i]) {
                        activeIndex = i;
                        break;
                    }
                    accumulated += slideHeights[i];
                }

                dots.forEach((dot, i) => {
                    if (i === activeIndex) dot.classList.add('active');
                    else dot.classList.remove('active');
                });
            }

            arrowUp.addEventListener('click', () => {
                const currentScroll = slider.scrollTop;
                const slideHeights = Array.from(slides).map(s => s.offsetHeight);
                let accumulated = 0;
                let prevSlideIndex = 0;
                for (let i = 0; i < slideHeights.length; i++) {
                    if (currentScroll > accumulated + slideHeights[i] / 2) {
                        accumulated += slideHeights[i];
                        prevSlideIndex = i + 1;
                    } else {
                        break;
                    }
                }
                if (prevSlideIndex > 0) {
                    slides[prevSlideIndex - 1].scrollIntoView({ behavior: 'smooth' });
                } else {
                    slides[0].scrollIntoView({ behavior: 'smooth' });
                }
            });

            arrowDown.addEventListener('click', () => {
                const currentScroll = slider.scrollTop;
                const slideHeights = Array.from(slides).map(s => s.offsetHeight);
                let accumulated = 0;
                let nextSlideIndex = slides.length - 1;
                for (let i = 0; i < slideHeights.length; i++) {
                    if (currentScroll < accumulated + slideHeights[i] - 10) {
                        nextSlideIndex = i;
                        break;
                    }
                    accumulated += slideHeights[i];
                }
                if (nextSlideIndex < slides.length - 1) {
                    slides[nextSlideIndex + 1].scrollIntoView({ behavior: 'smooth' });
                } else {
                    slides[slides.length - 1].scrollIntoView({ behavior: 'smooth' });
                }
            });

            slider.addEventListener('scroll', () => {
                requestAnimationFrame(updateActiveDot);
            });

            window.addEventListener('load', () => {
                setTimeout(updateActiveDot, 100);
            });

            window.addEventListener('resize', () => {
                updateActiveDot();
            });

            // Логика опроса
            const voteYes = document.getElementById('vote-yes');
            const voteNo = document.getElementById('vote-no');

            function handleVote(answer) {
                alert(`Спасибо за ваш голос! Вы выбрали: ${answer}`);
                // Здесь можно добавить сохранение результатов (например, в localStorage или отправку на сервер)
            }

            voteYes.addEventListener('click', () => handleVote('Да'));
            voteNo.addEventListener('click', () => handleVote('Нет'));
        })();
    </script>
</body>
</html>>
