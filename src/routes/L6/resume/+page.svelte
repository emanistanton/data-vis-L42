<script>
  import { base } from "$app/paths";
</script>

<svelte:head>
  <title>Resume — Emani Stanton</title>
</svelte:head>

<div class="resume-page">
  <main>
    <header class="resume-header">
      <h1>Emani Stanton</h1>
      <p>Phone: (678)-314-5737 · Email: <a href="mailto:emani04@mit.edu">emani04@mit.edu</a></p>
      <p>
        <a class="resume-download" href={`${base}/Emani%20Stanton%20-%20Resume%202026.pdf`} target="_blank"
          >Download PDF</a
        >
      </p>
    </header>

    <section class="resume-education" aria-labelledby="education">
      <h2 id="education">Education</h2>
      <p><strong>Massachusetts Institute of Technology (MIT)</strong> — <time>Class of 2026</time></p>
      <p>Bachelor of Computer Science and Engineering</p>
      <ul>
        <li>Scholar programs: Gates scholar, Jack Kent Cooke scholar, Achieve Atlanta</li>
        <li>Relevant coursework: Distributed systems, Computational structures, Discrete Mathematics, Algorithms</li>
      </ul>
    </section>

    <section class="resume-work" aria-labelledby="work-experience">
      <h2 id="work-experience">Work Experience</h2>

      <article>
        <header>
          <h3>Point72 — Quantitative Developer Intern</h3>
          <p><time>May 2025</time> - <time>Aug 2025</time> · New York City, NY</p>
        </header>
        <ul>
          <li>Upgraded and extended multi-level caching across workers in Ray's distributed computing framework.</li>
          <li>Expanded internal ML research tools to be compatible with the firm's on-prem central compute cluster.</li>
          <li>Unblocked and systemized Kerberos + AWS authentication across distributed jobs on Ray and AWS Fargate.
          </li>
          <li>Built tooling and resolved dependencies to create seamless transitions across compute environments.</li>
        </ul>
        <p><strong>Tools:</strong> Ray, AWS (Fargate, S3FS), Python, Kerberos, Docker</p>
      </article>

      <article>
        <header>
          <h3>PDT Partners — Software Engineering Intern</h3>
          <p><time>Jun 2024</time> - <time>Aug 2024</time> · New York City, NY</p>
        </header>
        <ul>
          <li>Designed and deployed a database search web app, working closely with internal business clients.</li>
          <li>Built a scalable database design with granular permissioning to support both contractors and employees.
          </li>
          <li>Deployed a database engine API for Snowflake to integrate data into other internal platforms.</li>
        </ul>
        <p><strong>Tools:</strong> Snowflake, Alembic, Flask, Python, React, SQL, TypeScript</p>
      </article>

      <article>
        <header>
          <h3>The Coding School — Software Engineering Intern</h3>
          <p><time>Jan 2024</time> - <time>Feb 2024</time> · Boston, MA</p>
        </header>
        <ul>
          <li>Built an auto-grading system for the non-profit's data science and CS courses, serving 1,000+ students.
          </li>
          <li>Integrated Canvas (grading platform) and Jupyter Notebook to store and automatically grade assignments.
          </li>
        </ul>
        <p><strong>Tools:</strong> Python, Jupyter Notebook, APIs</p>
      </article>

      <article>
        <header>
          <h3>Bloomberg — Software Engineering Intern</h3>
          <p><time>Jun 2023</time> - <time>Aug 2023</time> · New York City, NY</p>
        </header>
        <ul>
          <li>Redesigned and implemented an order staging method allowing users to create and use templates.</li>
          <li>Full Stack development including data design, API, SQL database, front-end integration, and testing.</li>
        </ul>
        <p><strong>Tools:</strong> Docker, Bloomberg Terminal, C++, CMake, Python, Javascript, SQL, REST API services,
          XSD, JSON Schema</p>
      </article>

      <article>
        <header>
          <h3>Nasdaq — Software Engineering Intern</h3>
          <p><time>Jan 2023</time> - <time>Feb 2023</time> · Boston, MA</p>
        </header>
        <ul>
          <li>Developed an internal portal for Nasdaq's AI Data Models supporting model execution and status reporting.
          </li>
          <li>Provided feedback notifications, model logging, search methods, simplified workflow, and easier auditing.
          </li>
          <li>Implemented data pipeline, UI, AWS lambda for secure file upload, and Terraform bucket for triggered batch
            jobs.</li>
        </ul>
        <p><strong>Tools:</strong> Angular, TypeScript, AWS, Terraform</p>
      </article>

    </section>

    <section class="resume-projects" aria-labelledby="projects">
      <h2 id="projects">Projects</h2>
      <article>
        <h3>LiveBet</h3>
        <p><time>Dec 2024</time> - Present</p>
        <ul>
          <li>Developed a system to exploit live arbitrage opportunities in sports books.</li>
          <li>Designed extremely fast response times &lt;5s from odds update to order completion.</li>
          <li>Solved complex infrastructure challenges, including cross-book wager matching and low-latency
            orchestration.</li>
          <li>Completed 150+ successful arbitrage orders making +$15,000 in profit in just 2 months.</li>
        </ul>
        <p><strong>Tools:</strong> Golang, C++, Python, Selenium, SQL</p>
      </article>
    </section>

    <section class="resume-awards" aria-labelledby="awards-skills">
      <h2 id="awards-skills">Awards &amp; Skills</h2>
      <ul>
        <li>Teen Vogue's 21 Under 21</li>
        <li>Harvard's International Debate Tournament - 1st place</li>
        <li>NCWIT - Honors recipient</li>
        <li>Microsoft's Black History Month Exhibit (2021)</li>
        <li>Southern Black Girls Conference - Honors Recipient</li>
      </ul>

      <h3>Languages &amp; Tools</h3>
      <p>Python · C++ · SQL · TypeScript · Golang</p>
      <p>Docker · AWS · React/Angular · CMake · Ray</p>

      <h3>Techniques</h3>
      <p>Database Design · Distributed Systems · CI/CD · DevOps · Testing</p>
    </section>
  </main>
</div>
