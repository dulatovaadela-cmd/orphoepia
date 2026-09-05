async function saveResult() {
  if (!attemptId.value) {
    saveStatus.value = "error"
    return
  }

  saveStatus.value = "saving"

  const completedAt = new Date().toISOString()

  const { error } = await supabase
    .from("attempts")
    .update({
      score: correctCount.value,
      percentage: percent.value,
      completed_at: completedAt,
    })
    .eq("id", attemptId.value)

  if (error) {
    console.error(
      "Ошибка сохранения результата:",
      error
    )
    saveStatus.value = "error"
    return
  }

  saveStatus.value = "saved"
}
