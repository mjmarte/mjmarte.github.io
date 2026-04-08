---
layout: default
---

<div class="profile-card">
<div class="profile-hover" id="profile-hover">
<img class="profile-picture" src="profile.jpg" alt="Manuel Jose Marte">
<img class="profile-gif" src="eyes-moving.gif" alt="Manuel Jose Marte animated">
</div>
<p class="profile-cta">Interested in working together? Reach out:</p>
<div class="social-icons">
<a href="mailto:mmarte1@jh.edu" title="Email"><i class="fas fa-envelope"></i></a>
<a href="https://orcid.org/0000-0002-1837-601X" title="ORCID"><i class="ai ai-orcid"></i></a>
<a href="https://scholar.google.com/citations?user=R3M1K-MAAAAJ" title="Google Scholar"><i class="ai ai-google-scholar"></i></a>
<a href="https://bsky.app/profile/mjm.bsky.social" title="Bluesky"><i class="fa-brands fa-bluesky"></i></a>
<a href="https://twitter.com/manueljmarte" title="Twitter / X"><i class="fa-brands fa-x-twitter"></i></a>
</div>
</div>

<script>
(function() {
  var gifs = [
    { src: "eyes-moving.gif", duration: 18240 },
    { src: "brain.gif", duration: 7840 }
  ];
  var el = document.getElementById("profile-hover");
  var gifImg = el.querySelector(".profile-gif");
  var timer = null;
  var hovering = false;
  var currentIndex = -1;

  // Preload all GIFs so transitions are instant
  gifs.forEach(function(g) {
    var img = new Image();
    img.src = g.src;
  });

  function pickRandom() {
    if (gifs.length <= 1) return 0;
    var idx;
    do { idx = Math.floor(Math.random() * gifs.length); } while (idx === currentIndex);
    return idx;
  }

  function showGif(index) {
    currentIndex = index;
    var g = gifs[index];
    // Reset the GIF by reloading with a cache-bust, but image is already cached
    gifImg.src = g.src + "?t=" + Date.now();
    gifImg.style.opacity = 1;
    timer = setTimeout(function() {
      if (hovering) {
        var next = (index + 1) % gifs.length;
        showGif(next);
      }
    }, g.duration);
  }

  el.addEventListener("mouseenter", function() {
    hovering = true;
    showGif(pickRandom());
  });

  el.addEventListener("mouseleave", function() {
    hovering = false;
    clearTimeout(timer);
    timer = null;
    gifImg.style.opacity = 0;
    currentIndex = -1;
  });
})();
</script>

Language disorders are among the most common and consequential sequelae of stroke, yet the standard tools used to assess and predict recovery remain limited. My research applies computational methods to the study of aphasia to improve its assessment, treatment, and prognosis.

I am a Postdoctoral Research Fellow with Dr. [Argye E. Hillis](https://score.jhmi.edu/director.html) in the [SCORE Lab](https://score.jhmi.edu/index.html) ([Department of Neurology](https://www.hopkinsmedicine.org/neurology-neurosurgery), Johns Hopkins University) and a T32 NRSA Fellow at [Kennedy Krieger Institute](https://www.kennedykrieger.org). Here, I apply natural language processing, machine learning, and neuroimaging to study post-stroke language at multiple linguistic levels and across the trajectory of recovery.

I completed my Ph.D. in [Speech, Language & Hearing Sciences](https://www.bu.edu/sargent/academics/departments-programs/speech-language-hearing-sciences/phd-in-slhs/), working with Dr. [Swathi Kiran](https://www.bu.edu/sargent/profile/swathi-kiran-ph-d-ccc-slp/) ([Center for Brain Recovery](https://www.bu.edu/cbr/), Boston University) and Dr. [Einat Liebenthal](https://www.mcleanhospital.org/profile/einat-liebenthal) ([Institute for Technology in Psychiatry](https://bakerlab.mclean.harvard.edu/institute-for-technology-in-psychiatry/), McLean Hospital, Harvard Medical School). 

Previously, I worked as a speech-language pathologist specializing in TBI and stroke neurorehabilitation at the [Northeast Center for Brain Injury and Rehabilitation](https://www.northeastcenter.com). I hold an M.S. from [SUNY New Paltz](https://www.newpaltz.edu/commdis/) and a B.A. from the [University at Buffalo](https://arts-sciences.buffalo.edu/cds.html).

When I am not working, I am spending time with my family, shooting hoops, and [reading](https://oku.club/user/mjm).
