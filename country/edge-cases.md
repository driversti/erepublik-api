# Edge Cases and Empty States

#erepublik #api #country #edge-cases

[< Back to Country Overview](README.md)

---

Country pages handle various edge states differently. These patterns apply across all tab types and are useful for building robust parsers.

## Society Page -- No Regions (0 Regions)

When a country has lost all its territory (all regions conquered), the regions section shows an empty table:

<details>
<summary>HTML example</summary>

```html
<h2 class="section" id="currentRegions">Regions (0)</h2>
<div class="indent">
    <table class="regions"><tr>
        <th width="490px"></th>
        <th width="65px"></th>
    </tr>
    </table>
</div>
```

</details>

See [society.md](society.md) for full regions table documentation.

## Society Page -- No Alliance

When a country has no alliance, the `<a class="alliance_name">` element is simply absent from the `#profileholder`:

<details>
<summary>HTML example</summary>

```html
<div id="profileholder">
    <h1>Albania</h1>
    <p class="padded">
        <a href="//wiki.erepublik.com/index.php/Albania" target="_blank" class="goleft rightpadded">...</a>
        <a class="country_rank" href="...">Rank 25</a>
        <!-- No alliance_name element -->
    </p>
</div>
```

</details>

See [society.md](society.md) for full country header documentation.

## Politics Page -- Dissolved Congress (Dictatorship)

Under dictatorship, the Congress table has headers but **no party rows**:

<details>
<summary>HTML example</summary>

```html
<table class="political largepadded">
    <tr>
        <th width="210px"></th>
        <th width="85px"></th>
        <th width="305px"></th>
    </tr>
    <!-- No party rows -- Congress is dissolved -->
</table>
```

</details>

See [politics.md](politics.md) for full Congress section documentation and the Dictator section that appears in its place.

## Military Page -- No Active Wars

<details>
<summary>HTML example</summary>

```html
<tr>
    <td colspan="2">
        This country is not involved in any war.
    </td>
</tr>
```

</details>

See [military.md](military.md) for full active wars documentation.

## Military Page -- No Resistance Wars

<details>
<summary>HTML example</summary>

```html
<td colspan="3">There are no resistance wars in this country.</td>
```

</details>

See [military.md](military.md) for full resistance wars documentation.

## Military Page -- No Mutual Protection Pacts

<details>
<summary>HTML example</summary>

```html
<td colspan="3">This country has no mutual protection pacts.</td>
```

</details>

See [military.md](military.md) for full MPP documentation.

---

## See Also

- [Country Society Page](society.md)
- [Country Politics Page](politics.md)
- [Country Military Page](military.md)
- [Country Administration Page](administration.md)
