<script type="text/javascript">
  // Register the custom BBCode parser
  Discourse.Markdown.on("parse", function (doc) {
    const embedRegex = /\[embedtopic id=(\d+)\]/g;
    let match;

    while ((match = embedRegex.exec(doc.text)) !== null) {
      const topicId = match[1];
      const placeholder = `<div class="embedded-topic-placeholder" data-topic-id="${topicId}">Loading topic ${topicId}...</div>`;
      doc.text = doc.text.replace(match[0], placeholder);
    }
  });

  // Render the embedded topic after the post is cooked
  Discourse.on("afterCook", function (cookedElement, opts, post) {
    const placeholders = cookedElement.querySelectorAll(".embedded-topic-placeholder");

    placeholders.forEach(async (placeholder) => {
      const topicId = placeholder.dataset.topicId;

      // Check for max topics setting (if you implemented it in settings.yml)
      // const maxTopics = settings.embed_topic_max_topics;
      // if (cookedElement.querySelectorAll(".embedded-topic-container").length >= maxTopics) {
      //   placeholder.innerHTML = `<div class="embedded-topic-error">Maximum embedded topics reached.</div>`;
      //   return;
      // }

      try {
        const response = await fetch(`/t/${topicId}.json`); // Discourse API endpoint for topics
        if (!response.ok) {
          throw new Error(`HTTP error! status: ${response.status}`);
        }
        const topicData = await response.json();

        if (topicData && topicData.post_stream && topicData.post_stream.posts && topicData.post_stream.posts.length > 0) {
          const firstPost = topicData.post_stream.posts[0];
          const topicTitle = topicData.title;
          const topicUrl = topicData.url; // Use topicData.url for the full URL

          // Construct the embedded topic HTML
          const embeddedHtml = `
            <div class="embedded-topic-container">
              <div class="embedded-topic-header">
                <a href="${topicUrl}" target="_blank" rel="noopener noreferrer">${topicTitle}</a>
              </div>
              <div class="embedded-topic-content">
                ${firstPost.cooked}
              </div>
            </div>
          `;
          placeholder.outerHTML = embeddedHtml; // Replace the placeholder with the actual content
        } else {
          placeholder.outerHTML = `<div class="embedded-topic-container"><div class="embedded-topic-error">Topic ID ${topicId} not found or has no content.</div></div>`;
        }
      } catch (error) {
        console.error(`Error embedding topic ${topicId}:`, error);
        placeholder.outerHTML = `<div class="embedded-topic-container"><div class="embedded-topic-error">Error loading topic ${topicId}.</div></div>`;
      }
    });
  });
</script>
