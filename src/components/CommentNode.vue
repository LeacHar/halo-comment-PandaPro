<template>

  <li
     :id="'li-comment-'+comment.id"
     class="comment even thread-even depth-1"
     itemprop="comment">
    <article id="div-comment-95" class="comment-body d-flex flex-fill "><div class="comment-avatar-author vcard mr-2 mr-md-3 ">
      <div class="flex-avatar w-48"><img
              :alt="comment.author+`'s avatar`"
              :src="avatar"
              class="avatar"
              width="48" height="48"></div>
    </div>
      <!-- .comment-author -->
      <div class="comment-text d-flex flex-fill flex-column">
        <div class="comment-info d-flex align-items-center mb-1">
          <div class="comment-author text-sm">
            <a target="_blank" :href="comment.authorUrl" rel="external nofollow ugc" class="url">{{ comment.author }}</a>                </div>
        </div>
        <div
                class="comment-content d-inline-block text-sm"
                itemprop="description"
                v-html="compileContent"
        >
        </div>
        <!-- .comment-content -->
        <div class="d-flex flex-fill text-xs text-muted pt-2">
          <div>
            <time
                    class="comment-date"
                    itemprop="datePublished"
                    :datetime="comment.createTime"
            >{{ createTimeAgo }}</time>
          </div>
          <div class="flex-fill"></div>
          <a
                  class="comment-reply-link"
                  style="cursor: pointer;"
                  rel="nofollow"
                  :style="editing?'display:block;':''"
                  @click="handleReplyClick"
          >{{ editing?'取消回复':'回复' }}</a>
        </div>
      </div>
      <!-- .comment-text -->
    </article><!-- .comment-body -->

    <comment-editor
            v-if="editing"
            :targetId="targetId"
            :target="target"
            :replyComment="comment"
            :options="options"
            :configs="configs"
    />
    <ul
            v-if="comment.children"
            class="children"
    >
      <template v-for="(children, index) in comment.children">
        <CommentNode
                :isChild="true"
                :targetId="targetId"
                :target="target"
                :comment="children"
                :options="options"
                :configs="configs"
                :key="index"
        />
      </template>
    </ul>
    <!-- .children -->
  </li>

</template>
<script>
import "./index";
import { timeAgo } from "@/utils/util";
import ua from "ua-parser-js";
import marked from "marked";
export default {
  name: "CommentNode",
  props: {
    isChild: {
      type: Boolean,
      required: false,
      default: false
    },
    targetId: {
      type: Number,
      required: false,
      default: 0
    },
    target: {
      type: String,
      required: false,
      default: "posts",
      validator: function(value) {
        return ["posts", "sheets", "journals"].indexOf(value) !== -1;
      }
    },
    comment: {
      type: Object,
      required: false,
      default: () => {}
    },
    options: {
      type: Object,
      required: false,
      default: () => {}
    },
    configs: {
      type: Object,
      required: true
    }
  },
  data() {
    return {
      editing: false
    };
  },
  computed: {
    avatar() {
      return (
        this.configs.gravatarSource +
        `/${this.comment.gravatarMd5}?s=256&d=` +
        this.options.comment_gravatar_default
      );
    },
    compileContent() {
      var at = "";
      return at + marked(this.comment.content);
    },
    createTimeAgo() {
      return timeAgo(this.comment.createTime);
    },
    compileUserAgent() {
      var parser = new ua();
      parser.setUA(this.comment.userAgent);
      var result = parser.getResult();
      return (
        result.browser.name +
        " " +
        result.browser.version +
        " in " +
        result.os.name +
        " " +
        result.os.version
      );
    }
  },
  methods: {
    handleReplyClick() {
      this.editing = !this.editing;
    }
  }
};
</script>
