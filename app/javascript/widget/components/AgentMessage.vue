<template>
  <div
    class="agent-message-wrap group"
    :class="{
      'has-response': hasRecordedResponse || isASubmittedForm,
    }"
  >
    <div v-if="!isASubmittedForm" class="agent-message">
      <div class="avatar-wrap">&nbsp;</div>
      <div class="message-wrap">
        <div v-if="hasReplyTo" class="flex mt-2 mb-1 text-xs">
          <reply-to-chip :reply-to="replyTo" />
        </div>
        <div class="flex gap-1">
          <div class="space-y-2">
            <AgentMessageBubble
              v-if="shouldDisplayAgentMessage"
              :content-type="contentType"
              :message-content-attributes="messageContentAttributes"
              :message-id="message.id"
              :message-type="messageType"
              :message="message.content"
            />
            <div
              v-if="hasAttachments"
              class="space-y-2 chat-bubble has-attachment agent"
              :class="(wrapClass, $dm('bg-white', 'dark:bg-slate-700'))"
            >
              <div
                v-for="attachment in message.attachments"
                :key="attachment.id"
              >
                <image-bubble
                  v-if="attachment.file_type === 'image' && !hasImageError"
                  :url="attachment.data_url"
                  :thumb="attachment.data_url"
                  :readable-time="readableTime"
                  @error="onImageLoadError"
                />

                <video-bubble
                  v-if="attachment.file_type === 'video' && !hasVideoError"
                  :url="attachment.data_url"
                  :readable-time="readableTime"
                  @error="onVideoLoadError"
                />

                <audio v-else-if="attachment.file_type === 'audio'" controls>
                  <source :src="attachment.data_url" />
                </audio>
                <file-bubble v-else :url="attachment.data_url" />
              </div>
            </div>
          </div>
          <div class="flex flex-col justify-end">
            <message-reply-button
              class="transition-opacity delay-75 opacity-0 group-hover:opacity-100 sm:opacity-0"
              @click="toggleReply"
            />
          </div>
        </div>
      </div>
    </div>

    <UserMessage v-if="hasRecordedResponse" :message="responseMessage" />
    <div v-if="isASubmittedForm">
      <UserMessage
        v-for="submittedValue in submittedFormValues"
        :key="submittedValue.id"
        :message="submittedValue"
      />
    </div>
  </div>
</template>