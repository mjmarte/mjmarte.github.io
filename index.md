---
layout: default
---

<div class="profile-hover" id="profile-hover">
  <img class="profile-picture" src="profile.jpg" alt="Manuel Jose Marte">
  <img class="profile-gif" src="eyes-moving.gif" alt="Manuel Jose Marte animated">
</div>

<script>
(function() {
  var gifs = ["eyes-moving.gif", "brain.gif"];
  var el = document.getElementById("profile-hover");
  var gifImg = el.querySelector(".profile-gif");
  var idx = 0;
  var timer = null;

  function showNext() {
    gifImg.style.opacity = 0;
    setTimeout(function() {
      idx = (idx + 1) % gifs.length;
      gifImg.src = gifs[idx];
      gifImg.style.opacity = 1;
    }, 300);
  }

  el.addEventListener("mouseenter", function() {
    gifImg.src = gifs[idx];
    gifImg.style.opacity = 1;
    timer = setInterval(showNext, 2000);
  });

  el.addEventListener("mouseleave", function() {
    clearInterval(timer);
    timer = null;
    gifImg.style.opacity = 0;
  });
})();
</script>

Few human capacities are as defining as language, so when neurological injury or disease impairs it, few losses are more devastating. My research seeks to advance our understanding of these impairments and to improve their assessment, prognosis, and treatment.

Currently, I am a Postdoctoral Research Fellow with Dr. [Argye E. Hillis](https://score.jhmi.edu/director.html) in the [SCORE (Stroke Cognitive Outcomes & REcovery) Lab](https://score.jhmi.edu/index.html) in the [Department of Neurology](https://www.hopkinsmedicine.org/neurology-neurosurgery) at Johns Hopkins University. My work applies machine learning and large language models to questions about how recovery unfolds and how connected speech can be analyzed both rigorously and at scale.

As a multilingual researcher, I am also drawn to questions about how our theories of language disorders can better reflect the variety of human linguistic experience.

I completed my Ph.D. in [Speech, Language & Hearing Sciences](https://www.bu.edu/sargent/academics/departments-programs/speech-language-hearing-sciences/phd-in-slhs/) at Boston University, working with Dr. [Swathi Kiran](https://www.bu.edu/sargent/profile/swathi-kiran-ph-d-ccc-slp/) at the [Center for Brain Recovery](https://www.bu.edu/cbr/) and Dr. [Einat Liebenthal](https://www.mcleanhospital.org/profile/einat-liebenthal) in the [Institute for Technology in Psychiatry](https://bakerlab.mclean.harvard.edu/institute-for-technology-in-psychiatry/) at McLean Hospital. My dissertation developed a naturalistic paradigm for aphasia assessment, investigating how language, emotion, and visual attention interact in contexts that better reflect real-world demands.

Prior to my doctoral training, I worked as a speech-language pathologist specializing in traumatic brain injury and stroke neurorehabilitation at the [Northeast Center for Brain Injury and Rehabilitation](https://www.northeastcenter.com). I earned my M.S. in Communication Disorders from [SUNY New Paltz](https://www.newpaltz.edu/commdis/) and my B.A. in Speech and Hearing Science from the [University at Buffalo](https://arts-sciences.buffalo.edu/cds.html).

When I’m not working, I’m spending time with my family, shooting hoops, and [reading](https://oku.club/user/mjm).

If you're interested in working together, please reach out:

<div class="social-icons">
  <a href="mailto:mmarte1@jh.edu" title="Email"><i class="fas fa-envelope"></i></a>
  <a href="https://orcid.org/0000-0002-1837-601X" title="ORCID"><i class="ai ai-orcid"></i></a>
  <a href="https://scholar.google.com/citations?user=R3M1K-MAAAAJ" title="Google Scholar"><i class="ai ai-google-scholar"></i></a>
  <a href="https://bsky.app/profile/mjm.bsky.social" title="Bluesky"><i class="fab fa-bluesky"></i></a>
  <a href="https://twitter.com/manueljmarte" title="Twitter / X"><i class="fab fa-x-twitter"></i></a>
</div>
